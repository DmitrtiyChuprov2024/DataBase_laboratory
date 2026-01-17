# Лабораторная работа №2

Практическое развертывание базы данных и работа с SQL

## Инсталляция базы данных на сервере

## facility
```sql
CREATE TABLE IF NOT EXISTS chuprov.facility
(
    facility_id integer NOT NULL DEFAULT nextval('chuprov.facility_facility_id_seq'::regclass),
    adress character varying(50) COLLATE pg_catalog."default" NOT NULL,
    name character varying(15) COLLATE pg_catalog."default" NOT NULL,
    CONSTRAINT facility_pkey PRIMARY KEY (facility_id)
)

TABLESPACE pg_default;

ALTER TABLE IF EXISTS chuprov.facility
    OWNER to student;
```

## laboratories
```sql
CREATE TABLE IF NOT EXISTS chuprov.laboratories
(
    laboratory_id integer NOT NULL DEFAULT nextval('chuprov.laboratories_laboratory_id_seq'::regclass),
    name character varying(20) COLLATE pg_catalog."default" NOT NULL,
    floor integer NOT NULL,
    facility_id integer NOT NULL DEFAULT nextval('chuprov.laboratories_facility_id_seq'::regclass),
    CONSTRAINT laboratories_pkey PRIMARY KEY (laboratory_id),
    CONSTRAINT laboratories_facility_id_fkey FOREIGN KEY (facility_id)
        REFERENCES chuprov.facility (facility_id) MATCH SIMPLE
        ON UPDATE CASCADE
        ON DELETE CASCADE
)

TABLESPACE pg_default;

ALTER TABLE IF EXISTS chuprov.laboratories
    OWNER to student;
```

## objects
```sql
CREATE TABLE IF NOT EXISTS chuprov.objects
(
    object_id integer NOT NULL DEFAULT nextval('chuprov.objects_object_id_seq'::regclass),
    name character varying(30) COLLATE pg_catalog."default" NOT NULL,
    level_acess integer NOT NULL,
    type character varying(40) COLLATE pg_catalog."default" NOT NULL,
    lab_id integer NOT NULL DEFAULT nextval('chuprov.objects_lab_id_seq'::regclass),
    CONSTRAINT objects_pkey PRIMARY KEY (object_id),
    CONSTRAINT objects_lab_id_fkey FOREIGN KEY (lab_id)
        REFERENCES chuprov.laboratories (laboratory_id) MATCH SIMPLE
        ON UPDATE CASCADE
        ON DELETE CASCADE
)

TABLESPACE pg_default;

ALTER TABLE IF EXISTS chuprov.objects
    OWNER to student;
```

## employees
```sql
CREATE TABLE IF NOT EXISTS chuprov.employees
(
    employee_id integer NOT NULL DEFAULT nextval('chuprov.employees_employee_id_seq'::regclass),
    name character varying(50) COLLATE pg_catalog."default" NOT NULL,
    responsibility character varying(50) COLLATE pg_catalog."default" NOT NULL,
    lab_id integer NOT NULL DEFAULT nextval('chuprov.employees_lab_id_seq'::regclass),
    hired date NOT NULL,
    birthday date NOT NULL,
    CONSTRAINT employees_pkey PRIMARY KEY (employee_id),
    CONSTRAINT employees_lab_id_fkey FOREIGN KEY (lab_id)
        REFERENCES chuprov.laboratories (laboratory_id) MATCH SIMPLE
        ON UPDATE CASCADE
        ON DELETE CASCADE,
    CONSTRAINT employees_responsibility_fkey FOREIGN KEY (responsibility)
        REFERENCES chuprov.responsibility (responsibility) MATCH SIMPLE
        ON UPDATE CASCADE
        ON DELETE CASCADE
)

TABLESPACE pg_default;

ALTER TABLE IF EXISTS chuprov.employees
    OWNER to student;
```

## responsibility
```sql
CREATE TABLE IF NOT EXISTS chuprov.responsibility
(
    responsibility character varying(50) COLLATE pg_catalog."default" NOT NULL,
    level_acess integer NOT NULL,
    CONSTRAINT responsibility_pkey PRIMARY KEY (responsibility)
)

TABLESPACE pg_default;

ALTER TABLE IF EXISTS chuprov.responsibility
    OWNER to student;
```
