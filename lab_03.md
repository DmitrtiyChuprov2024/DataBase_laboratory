# Лабораторная работа №3

Представления и процедуры

**Цель:** Освоение механизмов абстракции данных и программных модулей


## Создание представления `object_look`
```sql
CREATE OR REPLACE VIEW public.object_look
 AS
 SELECT objects.object_id,
    objects.name AS object,
    facility.name AS facility,
    laboratories.name,
    objects.level_acess,
    employees.name AS employee,
    responsibility.responsibility
   FROM chuprov.objects
     JOIN chuprov.laboratories ON objects.lab_id = laboratories.laboratory_id
     JOIN chuprov.employees ON laboratories.laboratory_id = employees.lab_id
     JOIN chuprov.facility ON laboratories.facility_id = facility.facility_id
     JOIN chuprov.responsibility ON employees.responsibility::text = responsibility.responsibility::text
  WHERE objects.level_acess = responsibility.level_acess;

ALTER TABLE public.object_look
    OWNER TO student;
```

Данное представление выводит информацию об объектах, с помощью которой можно найти где он находится, необходимый уровень доступа и должность которое обладает таким уровнем.

## Создание хранимой процедуры `add_objectntli`
```sql
CREATE OR REPLACE PROCEDURE chuprov.add_objectntli(
	IN object_name character varying,
	IN object_type character varying,
	IN level_acess integer,
	IN labor_id integer)
LANGUAGE 'sql'
AS $BODY$
	INSERT INTO chuprov.objects (name, level_acess, type, lab_id)
	VALUES (object_name, level_acess, object_type, labor_id);
$BODY$;
ALTER PROCEDURE chuprov.add_objectntli(character varying, character varying, integer, integer)
    OWNER TO student;
```

Данная процедура позволяет добавить запись в таблице "objects"
