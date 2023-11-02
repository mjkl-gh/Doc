# Resetting all sequences to max(primary_key)
```sql
DO
$$
DECLARE
	rec record;
BEGIN
	FOR rec IN
		select *
		from (
		  select
			tc.table_name,
			kcu.column_name,
			pg_get_serial_sequence(tc.table_name, kcu.column_name) as pk_seq
		  from information_schema.table_constraints tc
		  join information_schema.key_column_usage kcu
			on kcu.constraint_name = tc.constraint_name
		  where tc.constraint_type = 'PRIMARY KEY'
		) as meta
		where meta.pk_seq is not null
	LOOP
		EXECUTE 'SELECT pg_catalog.setval(''' || rec.pk_seq||''', MAX('||quote_ident(rec.column_name)||')) 
		FROM ' || quote_ident(rec.table_name)
		;
		RAISE NOTICE '%' , 'SELECT pg_catalog.setval(''' || rec.pk_seq||''', MAX('||quote_ident(rec.column_name)||')) 
		FROM ' || quote_ident(rec.table_name)
		;
   	END LOOP;
END
$$;		
```
