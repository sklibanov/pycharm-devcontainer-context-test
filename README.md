# pycharm-devcontainer-context-test
Testing that PyCharm's devcontainer.json support respects the context setting therein.

The expectation is that the devcontainer.json would run the equivalent of this:

```shell
docker build -f ./Dockerfile -t my-devcontainer-image ..
```

To make sure it worked correctly, expect something like this:
```shell
.devcontainer % docker run --rm -it --name my-devcontainer my-devcontainer-image
Hey, this works after all!
It looks like IDEA-343335 is fixed for real.
```

Unfortunately, before the bugfix (with PyCharm 2023.3.2), what happens is this:

```shell
Connecting to Docker... Connected
[+] Building 0.5s (6/6) FINISHED                                 docker:default
 => [internal] load build definition from Dockerfile                       0.0s
 => => transferring dockerfile: 255B                                       0.0s
 => [internal] load .dockerignore                                          0.0s
 => => transferring context: 2B                                            0.0s
 => [internal] load metadata for docker.io/library/python:3.9              0.5s
 => [internal] load build context                                          0.0s
 => => transferring context: 2B                                            0.0s
 => [1/2] FROM docker.io/library/python:3.9@sha256:3d9dbe78e1f45ed2eb525b  0.0s
 => ERROR [2/2] COPY hello.txt /                                           0.0s
------
 > [2/2] COPY hello.txt /:
------
Dockerfile:5
--------------------
   3 |     
   4 |     # Copy the hello.txt file into the root directory of the image
   5 | >>> COPY hello.txt /
   6 |     
   7 |     # When the container starts, cat the hello.txt file
--------------------
ERROR: failed to solve: failed to compute cache key: failed to calculate checksum of ref da826162-5f83-4c44-ae24-368be9b49e87::lrnh61vtnx38pvbvz6ftz3pzn: "/hello.txt": not found
Image build failed with exit code 1.
```

This is the same error that happens if you put `.` instead of `..` in the docker build line above, with the cwd in the `.devcontainer` directory.

