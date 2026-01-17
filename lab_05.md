# Лабораторная работа №5

**Тема:** Триггеры и аудит
**Цель:** Реализация бизнес-логики на уровне БД и системы аудита

## Задачи лабораторной работы

1. Триггеры каскадного удаления для связей “один-ко-многим”
2. Триггеры аудита изменений (`INSERT`, `UPDATE`, `DELETE`)
3. Создание таблицы-журнала для отслеживания изменений

---

## Создание таблицы аудита
```sql
CREATE TABLE IF NOT EXISTS chuprov.audit
(
    audit_id integer NOT NULL DEFAULT nextval('chuprov.audit_audit_id_seq'::regclass),
    table_name character varying(50) COLLATE pg_catalog."default" NOT NULL,
    operation character varying(10) COLLATE pg_catalog."default" NOT NULL,
    record_id integer NOT NULL,
    old_name character varying(50) COLLATE pg_catalog."default",
    new_name character varying(50) COLLATE pg_catalog."default",
    old_adress character varying(50) COLLATE pg_catalog."default",
    new_adress character varying(50) COLLATE pg_catalog."default",
    operation_time timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    username character varying(50) COLLATE pg_catalog."default" DEFAULT CURRENT_USER,
    CONSTRAINT audit_pkey PRIMARY KEY (audit_id)
)

TABLESPACE pg_default;

ALTER TABLE IF EXISTS chuprov.audit
    OWNER to student;
```
## Функция триггера для аудита изменений при добавлении, удалении или обновления
```sql
CREATE OR REPLACE FUNCTION chuprov.audit_delete_facility()
    RETURNS trigger
    LANGUAGE 'plpgsql'
    COST 100
    VOLATILE NOT LEAKPROOF
AS $BODY$
BEGIN
    IF TG_OP = 'INSERT' THEN
        INSERT INTO chuprov.audit (
            table_name, operation, record_id,
            new_name, new_adress
        ) VALUES (
            TG_TABLE_NAME, 'INSERT', NEW.facility_id,
            NEW.name, NEW.adress
        );
        
    ELSIF TG_OP = 'UPDATE' THEN
        INSERT INTO chuprov.audit (
            table_name, operation, record_id,
            old_name, new_name,
            old_adress, new_adress
        ) VALUES (
            TG_TABLE_NAME, 'UPDATE', NEW.facility_id,
            OLD.name, NEW.name,
            OLD.adress, NEW.adress
        );
    ELSIF TG_OP = 'DELETE' THEN
        INSERT INTO chuprov.audit (
            table_name, operation, record_id,
            old_name, old_adress
        ) VALUES (
            TG_TABLE_NAME, 'DELETE', OLD.facility_id,
            OLD.name, OLD.adress
        );
    END IF;
    
    RETURN NULL;
END;
$BODY$;

ALTER FUNCTION chuprov.audit_delete_facility()
    OWNER TO student;
```

