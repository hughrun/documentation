- - -
Title: Aktualizowanie instancji Date: 2021-04-13 Order: 2
- - -

When there are changes available in the production branch, you can install and get them running on your instance using the command `./bw-dev update`. Wykonuje ono kilka rzeczy:

- `git pull` wczytuje zaktualizowany kod z repozytorium git. Jeśli wystąpią konflikty, może być konieczne wykonanie `git pull` oddzielnie i rozwiązanie konfliktów przez ponownym wypróbowaniem skryptu `./bw-dev update`.
- `docker-compose build` ponownie kompiluje obrazy, co zapewnia zainstalowanie odpowiednich pakietów. This step takes a long time and is only needed when the dependencies (including pip `requirements.txt` packages) have changed, so you can comment it out if you want a quicker update path and don't mind un-commenting it as needed.
- `docker-compose exec web python manage.py migrate` runs the database migrations in Django
- `docker-compose exec web python manage.py collectstatic --no-input` loads any updated static files (such as the JavaScript and CSS)
- `docker-compose restart` reloads the docker containers

## Re-building activity streams

Feeds for each user are stored in Redis. To re-populate a stream, use the management command:

``` { .sh }
./bw-dev populate_streams
# Or use docker-compose directly
docker-compose run --rm web python manage.py populate_streams
```

If something has gone terribly awry, the stream data can be deleted.

``` { .sh }
docker-compose run --rm web python manage.py erase_streams
```