

```yaml
version: '2'
services:
  kl:
    image:  docker.io/jboss/keycloak
    hostname: kl
    environment:
      - KEYCLOAK_USER=admin
      - KEYCLOAK_PASSWORD=admin
    ports:
      - 9080:8080
      - 9443:8443
  pm:
    image:  postman/newman:ubuntu
    hostname: pm
    volumes:
      - ./ei-wiremock/test/postman:/etc/newman:rw


  wm:
    image: dhaks/wiremock-2.26.3:1.0.0
    hostname: wm
    environment:
       WIREMOCK_OPTS: "--global-response-templating"
       WIREMOCK_KEYSTORE_PASSWORD: "changeit"
    ports:
      - "9082:8082"
      - "9081:8081"
    volumes:
      - /opt/wiremock/files:/opt/wiremock/__files
      - /opt/wiremock/mappings:/opt/wiremock/mappings:rw
      - /etc/mule/certs:/etc/wiremock/certs
      - ./security:/opt/wiremock/security:rw
```