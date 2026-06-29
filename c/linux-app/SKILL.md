---
name: linux-app
description: Linux application development patterns - CLI tools, systemd services, and package management.
license: MIT
metadata:
  author: c-skills
  version: "1.0.0"
---

# Linux Application (C)

Linux application patterns for C development.

## Basic Linux Program

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
    printf("Hello from Linux!\n");
    printf("PID: %d\n", getpid());
    return 0;
}
```

## Daemon/Syslog

```c
#include <syslog.h>
#include <unistd.h>

int daemonize() {
    pid_t pid = fork();
    if (pid < 0) return -1;
    if (pid > 0) exit(0);
    
    setsid();
    chdir("/");
    umask(0);
    
    openlog("myapp", LOG_PID, LOG_DAEMON);
    syslog(LOG_INFO, "Daemon started");
    return 0;
}
```

## Signal Handling

```c
#include <signal.h>

void sigint_handler(int sig) {
    syslog(LOG_INFO, "Received SIGINT, exiting");
    closelog();
    exit(0);
}

int main() {
    signal(SIGINT, sigint_handler);
    signal(SIGTERM, sigint_handler);
    // ... main loop
}
```

## Systemd Service

```ini
# /etc/systemd/system/myapp.service
[Unit]
Description=My Application
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/myapp
Restart=always
User=myuser

[Install]
WantedBy=multi-user.target
```

## Package Management (Debian)

```makefile
# Makefile for .deb package
PREFIX ?= /usr/local
BINDIR = $(PREFIX)/bin

install:
    install -d $(DESTDIR)$(BINDIR)
    install -m 755 myapp $(DESTDIR)$(BINDIR)/myapp

clean:
    rm -f *.o myapp
```

## Makefile

```makefile
CC = gcc
CFLAGS = -Wall -Wextra -O2
LDFLAGS = -lpthread

SRCS = $(wildcard *.c)
OBJS = $(SRCS:.c=.o)

myapp: $(OBJS)
	$(CC) -o $@ $^ $(LDFLAGS)

%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

install: myapp
	install -m 755 myapp /usr/local/bin/

.PHONY: clean install

clean:
	rm -f *.o myapp
```