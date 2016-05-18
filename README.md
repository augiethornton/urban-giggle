# urban-giggle
Setting up Canvas-lms to run in Docker


##### Docker Toolbox

Download link: https://www.docker.com/products/docker-toolbox

    Docker CLI client for running Docker Engine to create images and containers
    Docker Machine so you can run Docker Engine commands from Mac OS X terminals
    Docker Compose for running the docker-compose command
    Kitematic, the Docker GUI
    the Docker QuickStart shell preconfigured for a Docker command-line environment
    Oracle VM VirtualBox
    
If you used Toolbox for Mac to install Docker Machine, VirtualBox is automatically installed.


##### Dinghy

You'll want to walk through https://github.com/codekitchen/dinghy#install, but
when you run create, you may want to increase the system resources you give the
VM, like so:

```
$ dinghy create --memory=4096 --cpus=4 --provider=virtualbox
```


##### Building Canvas with Docker
With those dependencies installed, go to your Canvas directory and run
the following:

(this will take awhile as containers are built and downloaded.)

```
$ docker-compose build
```

The `docker-compose/config` directory has some config files already set up to use
the linked containers supplied by config. You can just copy them to
`config/`:

```
$ cp docker-compose/config/* config/
```

Getting things set up:

```
# install gems and dependencies
$ docker-compose run --rm web bundle install

# create db's defined in database.yml
$ docker-compose run --rm web bundle exec rake db:create

# seed and migrate your db
$ docker-compose run --rm web bundle exec rake db:initial_setup

# run migrations against your test db
$ docker-compose run --rm web bundle exec rake db:migrate RAILS_ENV=test

# compile Canvas assets
$ docker-compose run --rm web bundle exec rake canvas:compile_assets

# start your services
$ docker-compose up
```


##### Normal Usage

Normally you can just start everything with `docker-compose up`, and
access Canvas at http://canvas.docker/.

After pulling new code, you'll probably want to bundle install, run migrations and
update assets.

Changes you're making are not showing up?
Ctrl-C your `docker-compose up` window and restart.


##### Running tests

```
$ docker-compose run --rm web bundle exec rspec spec
```


##### Selenium

The container used to run the selenium browser is commented out of the
docker-compose file by default. To run selenium, just uncomment those lines,
rerun `docker-compose build`, and when you run your tests you can watch
the browser:

```
$ open vnc://secret:secret@selenium.docker/
```

##### Caveats

