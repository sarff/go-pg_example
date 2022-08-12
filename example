package db

import (
	"context"
	"fmt"
	"github.com/go-pg/pg/v10"
	"github.com/go-pg/pg/v10/orm"
	"github.com/sirupsen/logrus"
	"log"
	"olx-manager/pkg/utils"
	"time"
)

type Users struct {
	Id      int `pg:",pk"`
	Name    string
	Chat_id int64
}

func (u Users) String() string {
	return fmt.Sprintf("Users<%d %s %d>", u.Id, u.Name, u.Chat_id)
}

type OlxUserData struct {
	Id            int `pg:",pk"`
	Url           string
	Client_id     string `pg:",unique"`
	Client_secret string
	Refresh_token string
	UserID        int
	User          *Users `pg:"rel:has-one"`
}

func (u OlxUserData) String() string {
	return fmt.Sprintf("OlxUserData<%d %s %s %s %s %d>", u.Id, u.Url, u.Client_id, u.Client_secret, u.Refresh_token, u.UserID)
}

type QueueTask struct {
	Chat_id   int64 `pg:",unique"`
	Adv_id    int
	Timestamp time.Time `pg:"default:now()"`
}

func (u QueueTask) String() string {
	return fmt.Sprintf("Users<%d %d %s>", u.Chat_id, u.Adv_id, u.Timestamp)
}

func createSchema(db *pg.DB) error {
	models := []interface{}{
		(*Users)(nil),
		(*OlxUserData)(nil),
		(*QueueTask)(nil),
	}

	for _, model := range models {
		err := db.Model(model).CreateTable(&orm.CreateTableOptions{
			IfNotExists: true,
		})
		if err != nil {
			return err
		}
	}
	return nil
}

func Connections() *pg.DB {

	db := pg.Connect(&pg.Options{
		Addr:     utils.GoDotEnvVariable("POSTGRES_ADDR"),
		User:     utils.GoDotEnvVariable("POSTGRES_USER"),
		Password: utils.GoDotEnvVariable("POSTGRES_PASSWORD"),
		Database: utils.GoDotEnvVariable("POSTGRES_DB"),
	})

	//To check if database is up and running:
	ctx := context.Background()

	if err := db.Ping(ctx); err != nil {
		log.Fatal(err)
	}

	return db
}

func InitBase(log logrus.FieldLogger) {
	db := Connections()
	err := createSchema(db)
	if err != nil {
		log.Warningln(err)
	}
}
