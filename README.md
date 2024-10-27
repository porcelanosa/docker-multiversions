```bash
docker-compose up -d --build
```

### Enter to Docker console:

```bash
docker exec -it app bash
```
[docker exec](https://docs.docker.com/engine/reference/commandline/exec/)

Enter to Mysql docker container with password:

```bash
docker exec -it db mysql -uroot -p "qwerty123!wq"
```

Enter to NODE JS docker container:

```bash
docker exec -it node sh
```


