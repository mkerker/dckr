## Lab 4: Restarting and Attaching to Containers

--------

#### Objectives

We have started containers in the foreground, and in the background. In this lab, we will see how to:

-   Put a container in the background.

-   Attach to a background container to bring it to the foreground.

-   Restart a stopped container.

#### Background and foreground

The distinction between foreground and background containers is arbitrary. From Docker's point of view, all containers are the same.

All containers run the same way, whether there is a client attached to them or not. It is always possible to detach from a container, and to reattach to a container.

Analogy: attaching to a container is like plugging a keyboard and screen to a physical server.

--------

#### Step 1: Detaching from a container

-   If you have started an *interactive* container (with option `-it`), you can detach from it.

-   The "detach" sequence is ^P^Q (ctrl-P ctrl-Q)

-   Otherwise you can detach by killing the Docker client.

 (But not by hitting ^C, as this would deliver SIGINT to the container.)

What does `-it` stand for?

-   `-t` means "allocate a terminal."

-   `-i` means "connect stdin to the terminal."

#### Specifying a custom detach sequence

-   You don't like ^P^Q? No problem!

-   You can change the sequence with `docker run --detach-keys`.

-   This can also be passed as a global option to the engine.

Start a container with a custom detach command:
```
 $ docker run -it --detach-keys ctrl-x,x hcscompany/clock
```
Detach by hitting ^X x. (This is ctrl-x then x, not ctrl-x twice!)

Check that our container is still running:
```
 $ docker ps -l
```

--------

#### Step 2: Attaching to a container

You can attach to a container by `docker attach <containerID>`

-   The container must be running.

-   There *can* be multiple clients attached to the same container.

-   If you don't specify `--detach-keys` when attaching, it defaults back to ^P^Q.

Try it on our previous container:
```
 $ docker attach $(docker ps -lq)
```
Check that ^X x doesn't work, but ^P ^Q does. Let's try ^P ^Q for now.

#### Detaching from non-interactive containers

-   **Warning:** if the container was started without `-it`...

    -   You won't be able to detach with ^P^Q.

    -   If you hit ^C, the signal will be proxied to the container.

-   Remember: you can always detach by killing the Docker client.

--------

#### Step 3: Checking container output

Use `docker attach` if you intend to send input to the container.

If you just want to see the output of a container, use `docker logs  --tail 1 --follow <containerID>`, we used this in our previous lab.

--------
 
#### Step 4: Restarting a container

Use `docker attach` again and **stop** the container with ^C.

When a container has exited, it is in stopped state.
It can then be restarted with the start command: 

```
 $ docker start <yourContainerID>
```

The container will be restarted using the same options you launched it with.

You can re-attach to it if you want to interact with it:
```
 $ docker attach <yourContainerID>
```
Use `docker ps -a` to identify the container ID of a previous hcscompany/clock container, and try those commands.


