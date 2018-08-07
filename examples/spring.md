# Spring [](alias "spring")

**Scroll down for the JSON representation of the data layer.**

Here the we describe the core Spring settings of our application.

## Application [](alias "application")

The **.name** [](right) of the application is [ExampleApplication](string). It's just a simple example application to show off, how naturally we can mix documentation and data in downson.

In this example **.admin** [](right:object) features are not needed. Therefore the **.enabled** [](right) property is turned [off](boolean "false"). []($)

## Banner [](alias "banner")

The **.location** [](right) where the banner can be found is [classpath:banner.txt](string). It is encoded using the [UTF-8](string) **.charset** [](left). We've experimented with other charsets, but this one turned out to be the best one regarding portability.


# Embedded Server Configuration [](alias "server")

  * The server should listen on **.port** [](right) [8080](int). Please make sure, that other services do not use this port.
  * Currently, the **.timeout** [](right "connection-timeout") is [-1](int), which denotes an infinite amount. This should **NOT** be used in production.

---

This file defines the following *data layer*, when represented as a YAML document:

~~~~YAML
spring:
    application:
        name: ExampleApplication
        admin:
            enabled: false

    banner:
        location: classpath:banner.txt
        charset: UTF-8
    
server:
    port: 8080
    connection-timeout: -1
~~~~
