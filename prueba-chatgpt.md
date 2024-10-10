Para otorgar permisos completos a un usuario sobre toda la base de datos y evitar errores de permisos como el que mencionas, puedes seguir estos pasos en PostgreSQL:

1. **Otorgar todos los privilegios sobre todas las tablas existentes** en todos los esquemas. Esto se puede hacer con un comando `GRANT` global para todas las tablas, secuencias y funciones ya creadas.
   
   ```sql
   DO $$
   DECLARE
     r RECORD;
   BEGIN
     FOR r IN
       SELECT schemaname, tablename FROM pg_tables WHERE schemaname NOT IN ('pg_catalog', 'information_schema')
     LOOP
       EXECUTE 'GRANT ALL PRIVILEGES ON TABLE ' || quote_ident(r.schemaname) || '.' || quote_ident(r.tablename) || ' TO usr_gestionar';
     END LOOP;

     FOR r IN
       SELECT schemaname, sequencename FROM pg_sequences WHERE schemaname NOT IN ('pg_catalog', 'information_schema')
     LOOP
       EXECUTE 'GRANT ALL PRIVILEGES ON SEQUENCE ' || quote_ident(r.schemaname) || '.' || quote_ident(r.sequencename) || ' TO usr_gestionar';
     END LOOP;

     FOR r IN
       SELECT nspname, proname FROM pg_proc JOIN pg_namespace ON pg_proc.pronamespace = pg_namespace.oid WHERE nspname NOT IN ('pg_catalog', 'information_schema')
     LOOP
       EXECUTE 'GRANT ALL PRIVILEGES ON FUNCTION ' || quote_ident(r.nspname) || '.' || quote_ident(r.proname) || ' TO usr_gestionar';
     END LOOP;
   END $$;
   ```

2. **Otorgar permisos sobre los futuros objetos**. Para que el usuario también tenga permisos automáticamente sobre cualquier tabla, secuencia o función que se cree en el futuro, debes usar `ALTER DEFAULT PRIVILEGES`.

   Para tablas, secuencias y funciones:

   ```sql
   ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL PRIVILEGES ON TABLES TO usr_gestionar;
   ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL PRIVILEGES ON SEQUENCES TO usr_gestionar;
   ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL PRIVILEGES ON FUNCTIONS TO usr_gestionar;
   ```

   Repite esto para cualquier otro esquema diferente a `public` que estés utilizando.

3. **Otorgar privilegios globales** sobre la base de datos (opcional, si el usuario necesita realizar cambios como crear esquemas, tablas, etc.).

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE tu_base_de_datos TO usr_gestionar;
   ```

Con estos comandos, deberías cubrir tanto los objetos existentes como los futuros en la base de datos, y así evitar errores de permisos faltantes.

Asegúrate de cambiar `usr_gestionar` por el nombre correcto del usuario que estés utilizando y `tu_base_de_datos` por el nombre de tu base de datos.
