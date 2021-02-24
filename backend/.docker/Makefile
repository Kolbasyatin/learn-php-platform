.DEFAULT_GOAL := all
dockerfolder := .
zappstorefolder := ..

docker-compose := docker-compose --env-file=$(dockerfolder)/.env -f $(dockerfolder)/docker-compose.yaml
cli-compose-run := $(docker-compose) run --rm php
node-compose := docker-compose -f $(dockerfolder)/node-docker-compose.yml
node-compose-run := $(node-compose) run --rm node

#colors
grey := \033[0;37m
red := \033[0;31m
green := \033[0;32m
white := \033[0m


all:
	@echo "make install"

run:
	@$(cli-compose-run) $(filter-out $@,$(MAKECMDGOALS))

#install: check_vars docker_restart zappstore_setup do_migrations

install: check_vars docker_build docker_start app_setup app_migrations
check_vars: docker_check_vars

docker_check_vars:
	@echo -e "Check docker config"
	@test ! -f $(dockerfolder)/.env && (echo -e "$(green)Copy .env file.$(grey)"; cp -v $(dockerfolder)/.env.dist $(dockerfolder)/.env) || echo -e "$(green)File .env exists.$(white)"
	@test ! -f $(dockerfolder)/php/conf.d/php-ini-overrides.ini && (echo -e "$(green)Copy php-ini-overrides file.$(grey)"; cp -v $(dockerfolder)/php/conf.d/php-ini-overrides.ini.dist $(dockerfolder)/php/conf.d/php-ini-overrides.ini) || echo -e "$(green)File php-ini-overrides exists.$(white)"

docker_build: build
build:
	@$(docker-compose) build

###> docker ###
docker_start:
	@echo -e "$(green)Start docker services..."
	@$(docker-compose) up -d --remove-orphans && echo -e "$(green)Docker services was started." || 	(echo -e "$(red)Containers start failed!"; exit 1)

docker_stop:
	@echo -e "$(green)Stop docker services..."
	@$(docker-compose) down &&	echo -e "$(green)Docker containers was stopped." || (echo -e "$(red)Containers stopped failed!"; exit 1)
###< docker ###

###> application ###
app_setup:
	@echo -e "$(grey)Install application."
	@echo -e "$(green)Composer install."
	@$(cli-compose-run) composer install --no-interaction --no-scripts --no-progress --no-ansi
	@$(cli-compose-run) bin/console cache:clear
	@$(cli-compose-run) bin/console assets:install
	@$(node-compose-run) yarn install
	@$(node-compose-run) yarn encore dev

app_migrations:
	@echo 'Миграции при необходимости нужно запустить руками.'
###< application ###
