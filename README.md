# Documentación Assesment Pragma 2024-03

## Introducción

Habiendo analizado a fondo el problema propuesto, se planteó la creación de 3 proyectos back-end: El primero sería el encargado de almacenar y procesar la información relacionada con los clientes, el segundo todo lo relacionado con los productos financieros, y el último en cambio tiene la tarea de recibir la información de un cliente así como los productos financieros y retornar entonces cuáles de estos productos son aptos para el cliente en cuestión dadas las reglas establecidas para cada uno de estos productos.

#### <a href="collections/FinanceService_collection.json" download>Descargar colección Postman</a>

## Proyectos

### [→ AP2024_integration_nifi](https://github.com/fcordonezo/AP202403_integration_nifi)

Proyecto encargado de realizar la integración entre los componentes y entregar una respuesta procesada al consumidor final de la solución.
Desarrollado utilizando el framework de integración [Apache NIFI](https://nifi.apache.org/).  
#### [Ver Swagger](https://fcordonezo.github.io/AP202403_integration_nifi/)

### [→ AP2024_customer](https://github.com/fcordonezo/AP202403_customer)

Proyecto encargado de procesar y almacenar la información relacionada a un consumidor.
Desarrollado utilizando Java 21 con [Spring Boot](https://spring.io/projects/spring-boot).  
#### [Ver Swagger](https://fcordonezo.github.io/AP202403_customer/)

### [→ AP2024_finance_product](https://github.com/fcordonezo/AP202403_finance_product)

Proyecto encargado de procesar y almacenar la información relacionada a todos los productos financieros que operan en la solución.
Desarrollado utilizando Java 21 con [Spring Boot](https://spring.io/projects/spring-boot).  
#### [Ver Swagger](https://fcordonezo.github.io/AP202403_finance_product/)

### [→ AP2024_decision_engine](https://github.com/fcordonezo/AP202403_decision_engine)

Proyecto encargado de procesar, dado un cliente y una lista de productos financieros, cuál de estos productos aplica para el cliente especificado.
Desarrollado utilizando Java 21 con [Spring Boot](https://spring.io/projects/spring-boot).  
#### [Ver Swagger](https://fcordonezo.github.io/AP202403_decision_engine/)

## Funcionalidad

A continuación se explica la funcionalidad completa de la solución con un diagrama de secuencia:
![Diagrama de secuencia](./diagrams/secuence.svg)

En primer lugar el consumidor realiza la petición al componente de integración pasando en el path el id del cliente. A continuación este componente lanza pediciones ascincronas tanto a customer como a finance_product con el fin de obtener los datos requeridos. Luego se combinan estos dos json en uno solo que sea entendible por el siguiente componente, el cual es el decision_engine. Procesa la lógica necesaria y retorna la misma estructura json pero con los productos fiancieros filtrados.

## Objetos de negocio
![Modelo canónico](./diagrams/canonical.svg)

## Tablas de bases de datos
![Tablas bases de datos](./diagrams/database_tables.svg)

## Modelo de despliegue objetivo

Actualmente el proyecto se encuenta únicamente en local, pero el modelo objetivo de despliegue a futuro es el siguiente:
![Modelo objetivo](./diagrams/target.svg)

En donde se tiene un cluster de EKS en donde deberían estar desplegados todos los componentes en Deploys separados y con una política de HPA. El componente de observabilidad de Prometheus + Grafana también estaría dispuesto dentro de un Deploy independiente.  
Aparte, bases de datos RDS para el almacenamiento de información persistente (customers y financeProducts).  
Un secrets manager para almacenar los secretos de cada uno de los servicios.  
Finalmente una ruta de entrada a través de un Route53, luego por un WAF y finalmente un ALB.

## Diagrama de Componentes y Conectores
![Diagrama componentes y conectores](./diagrams/components_connectors.svg)

En este diagrama se pueden notar las conexiones que realizan los componentes a lo largo de la solución.

## Observabilidad

Se implementó observabilidad utilizando Prometheus con Grafana, a través de un controlador especial de Apache NIFI

## Seguridad

Desde el consumidor hacia NIFI se implementó TLS, y desde NIFI hacia los servicios back-end se implementó JWT. La información detallada de cada implementación está dispuesta en la documentación particular de cada componente.