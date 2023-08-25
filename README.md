# pontos-platform
Base platform configuration for a generic vessel sending data to [PONTOS](https://pontos.ri.se).

The platform setup is heavily reliant on [porla](https://github.com/MO-RISE/porla) and consist of multiple microservices for sourcing, transforming and pushing line-based data. These are organized into a set of `docker-compose.<xyz>.yml` files (stacks), grouped by data source and/or function. Depending on the hardware setup onboard the vessel, one, multiple or all of these files can be started, see below for details on specific requirements for each individual stack.

Configuration of the setup is done in an accompanying `.env` file. An [`example.env]`(./example.env) is provided in the repository.

## `docker-compose.n2k.yml`

**Requirements:**

* One (or several) [Yacht Device YDEN-02](https://www.yachtd.com/products/ethernet_gateway.html) or similar that outputs raw N2K frames (hex representation) over udp.

**Functionality:**

* Decodes raw N2K frames using [porla-nmea](https://github.com/MO-RISE/porla-nmea) (which in turn uses [canboat](https://github.com/canboat/canboat)) into JSON represenations.
* Transforms canboat JSON structs into PONTOS format using [porla-pontos](https://github.com/MO-RISE/porla-pontos) and rate limits the data flow to 1Hz for every unique "tag".
* Pushes PONTOS formatted data to pontos.ri.se using [porla-mqtt](https://github.com/MO-RISE/porla-mqtt).
* Logs raw N2K frames, canboat JSON structs and PONTOS data to three separate log files.

How-to:

Ensure you have a proper `.env` file configured and
```
docker compose -f docker-compose.n2k.yml up -d
```

You may have to use `sudo` depending on permissions.


## `docker-compose.logrotate.yml`

**Requirements:**

**Functionality:**

* Rotates all log files according to the specified `logrotate.conf` at midnight every day. 

How-to:

Ensure you have a proper `.env` file configured and
```
docker compose -f docker-compose.logrotate.yml up -d
```

You may have to use `sudo` depending on permissions.

