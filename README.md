# prologic

Personal website, blog and cv.

Uses [Hugo](https://gohugo.io/).

## Creating new content

```#!bash
$ hugo new post/<title>.md
```

or

```#!bash
$ hugo new page/<name>.md
```

## Testing

```#!bash
$ hugo server
```

## Deploying changes

1. Push changes

```#!bash
$ git push
```

2. Wait for the [Docker Hub](https://hub.docker.com) to rebuild the Docker image.

3. Update the service

```#!bash
$ export image="$(docker service inspect ${service} | jq -r '.[0] | .Spec.TaskTemplate.ContainerSpec.Image' | cut -f 1 -d '@')"
$ docker service update -d --image ${image} --with-registry-auth ${service}
```
