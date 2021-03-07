**Harshita Sharma Resume** 
# The csvserver assignment

Run the container image `infracloudio/csvserver:latest` in the background and check if it's running.

```
docker run -d --name <name of container> infracloudio/csvserver:latest 
docker container logs <name of container>
```
You will get this error:
```
error while reading the file "./inputdata": open ./inputdata: no such file or directory
```

Give execute permissions to `solution/gencsv.sh` and run it to generate a file named inputFile whose content looks like:
```csv
0, 234
1, 98
```
These are comma separated values with index and a random number.

Provide `numOfEntries` as argument to the script while executing, 
Running the script without any arguments, will generate the file `inputFile` with 10 entries in current directory.
```
chmod +x gencsv.sh
./gencsv.sh
```

Rename the `inputFile` to `inputdata`, as the container requires that file (as you will get in the error while first running the container, also mentioned above).
```
mv inputFile inputdata
```

Run the container again in the background with file `inputdata` available inside the container.
Replace the name of the container provided by --name argument in below command.
Here we are mounting the directory where `inputdata` is present i.e. /solution

```
docker run -d --name <name of container> -v $(pwd)/:/input infracloudio/csvserver:latest sh -c "cp /input/inputdata /csvserver ; /csvserver/csvserver
```

Get shell access to the container and find the port on which the application is listening. Once done, stop / delete the running container.
```
docker exec -it  <name of container> /bin/bash 
docker exec -it <name of container> sh -c "ss -tulwn | grep LISTEN "
docker stop <name of container>
docker rm <name of container>
```

Here we have added 2 more things:
- The application is accessible on the host at http://localhost:9393
- Set the environment variable `CSVSERVER_BORDER` to have value `Orange`.
Replace the name of the container as you please 

```
docker run -d  -e CSVSERVER_BORDER=Orange -v $(pwd)/:/input -p 9393:9300  --name <name of container> infracloudio/csvserver:latest sh -c "cp /input/inputdata /csvserver ; /csvserver/csvserver"
```

Access the application on the shell or in browser.
```
curl localhost:9393
```
