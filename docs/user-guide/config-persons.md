# Configuración y Formularios

Parametrización del sistema.

- Propiedades: `GET property/{type}/{field}`, `GET property/type/{type}/{filterName}`,
  `GET property/{key}`, `POST property/`
- Configuración: `GET configuration/export`, `POST configuration/module`,
  `POST configuration/import`, `POST configuration/compare` → `FileVO`

# Personas / Usuarios

Catálogo de usuarios.

- Listar: `POST user/getUsers`
- Por id: `GET user/{userId}`
- Por documento: `GET user/document/{documentId}`
- Propiedades: `GET user/properties/{userId}`
