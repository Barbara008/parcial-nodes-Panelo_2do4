# Parcial
## Panelo Barbara 2do 4ta

### Pasos proyecto:
### npm init -y
---

### npm i express

### npm i dotenv

### npm i sequelize mysql2

### npm i sequelize-cli --D

---
- Crear siguientes archivos en la raiz
>>.gitignore
>
>>.env
>
>>.sequelizerc
---
- .gitignore:
    /node_modules/
---
- .env:
        DB_USERNAME= root
        DB_PASSWORD=
        DB_HOST= localhost
        DB_DATABASE=nodesparcial
        DB_PORT=3306
        DB_DIALECT=mysql
---
- .sequelizerc:
    const path = require('path')
     module.exports = {
     config: path.resolve('./src/database/config', 'config.js'),
     'models-path': path.resolve('./src/database/models'),
     'seeders-path': path.resolve('./src/database/seeders'),
     'migrations-path': path.resolve('./src/database/migrations'),
     }
---
- Creamos carpeta **src**
- Creamos carpeta **public**
---
- Adentro de src creamos **app.js**
- Adentro de src creamos carpeta **controller**
- Adentro de src creamos carpeta **routes**
---
- En el Package.json que por defecto el main dice index.js lo cambio por **app.js**
- En **scrips** cambio "test" por **"dev"** y **"nodemon src/app.js"**
---
- En el archivo **app.js**:

         const express = require('express');
         const app = express();
         const path = require('path');

         const PORT = process.env.PORT || 3000

         app.use(express.static(path.resolve(__dirname, '../public')));

         app.use(express.json())
         //URL encode  - Para que nos pueda llegar la información desde el formulario al req.body
         app.use(express.urlencoded({ extended: false }));

         app.use('/', (req, res) => res.json({ clave: "Hola! desde el server" }));

         app.listen(PORT, () => {
             console.log('Servidor corriendo en el puerto ' + PORT)
         }
         );
---
- En la terminal escribir:
>sequelize init
-  para ejecutar **.sequelizerc**
-   eso creara la carpeta **database** y su contenido
---
- Dentro de
>src/database/config/config.js
- vamos a modificar todo y dentro pondremos lo siguiente:

- - require('dotenv').config()

     module.exports =

     {

         "username": process.env.DB_USERNAME,
         "password": process.env.DB_PASSWORD,
         "database": process.env.DB_DATABASE,
         "host": process.env.DB_HOST,
         "port": process.env.DB_PORT,
         "dialect": process.env.DB_DIALECT,

         seederStorage: "sequelize",
         seederStorageTableName: "seeds",

         migrationStorage: "sequelize",
         migrationStorageTableName: "migrations"

     }
---
- ## usaremos los siguientes comandos respetando el orden, primero los modelos sin clave foranea,
  ## al final los que contengan clave foranea.
---
>>sequelize model:generate --name Brand --attributes name:string
>
>>sequelize model:generate --name Category --attributes name:string
>
>>sequelize model:generate --name Size --attributes centimeter:integer
>
>>sequelize model:generate --name Gender --attributes type:string
>
>>sequelize model:generate --name Payment --attributes type:string
>
>>sequelize model:generate --name State --attributes description:string
>
>>sequelize model:generate --name Address --attributes street:string,number:integer
---
>>sequelize model:generate --name Product --attributes name:string,price:decimal,stock:integer,stock_min:integer,stock_max:integer,brands_id:integer,categories_id:integer,sizes_id:integer,genders_id:integer
>
>>sequelize model:generate --name User --attributes first_name:string,last_name:string,username:string,email:string,password:string,addresses_id:integer
---
>>sequelize model:generate --name Image --attributes name:string,products_id:integer
>
>>sequelize model:generate --name Order --attributes number:integer,date:date,total:decimal,payments_id:integer,users_id:integer,user_addresses_id:integer,states_id:integer
---
>>sequelize model:generate --name OrderDetail --attributes quantity:decimal,subtotal:decimal,products_id:integer,orders_id:integer
>
>>sequelize model:generate --name Shipping --attributes street:string,number:integer,orders_id:integer
---
- ## Aplicaremos asociaciones de HasOne, BelongsTo, HasMany cuando sea necesario
- ## Defino relaciones de modelos:
---
    static associate(models) {

      // belongsTo
      Product.belongsTo(models.Brand);
      // belongsTo
      Product.belongsTo(models.Category);
      // belongsTo
      Product.belongsTo(models.Size);
      // belongsTo
      Product.belongsTo(models.Gender);

      // hasMany
      Product.hasMany(models.Image, {
        foreignKey: 'products_id',
        as: "images"
      })

      // hasOne
      Product.hasOne(models.OrderDetail,{
        foreignKey: 'products_id',
        as: 'orderdetails'
      })
    }
---
    static associate(models) {

      // hasMany
      Brand.hasMany(models.Product, {
        foreignKey: 'brands_id',
        as: "products"
      })
    }
---
    static associate(models) {

      // hasMany
      Category.hasMany(models.Product, {
        foreignKey: 'categories_id',
        as: "products"
      })
    }
---
    static associate(models) {

      // hasMany
      Size.hasMany(models.Product, {
        foreignKey: 'sizes_id',
        as: "products"
      })
    }
---
    static associate(models) {

      // hasMany
      Gender.hasMany(models.Product, {
        foreignKey: 'genders_id',
        as: "products"
      })
    }
---
    static associate(models) {

      // belongsTo
      Image.belongsTo(models.Product);
    }
---
    static associate(models) {

      // belongsToOne
      OrderDetail.belongsTo(models.Product);

      // belongsTo
      OrderDetail.belongsTo(models.Order);
    }
---
    static associate(models) {

      // belongsToOne
      Order.belongsTo(models.Payment);
      // belongsToOne
      Order.belongsTo(models.State);
      
      // belongsTo
      Order.belongsTo(models.User);

      // hasMany
      Order.hasMany(models.OrderDetail, {
        foreignKey: 'orders_id',
        as: "orderdetails"
      })

      // hasOne
      Order.hasOne(models.Shipping, {
        as: 'shippings',
        foreignKey: 'orders_id'
      })
    }
---
    static associate(models) {

      // hasOne
      Payment.hasOne(models.Order, {
        as: 'orders',
        foreignKey: 'payments_id'
      })
    }
---
    static associate(models) {

      // hasOne
      State.hasOne(models.Order, {
        as: 'orders',
        foreignKey: 'states_id'
      })
    }
---
    static associate(models) {

      // hasMany
      User.hasMany(models.Order, {
        foreignKey: 'users_id',
        as: "orders"
      })

      // belongsToOne
      User.belongsTo(models.Address);
    }
---
    static associate(models) {

      // belongsToOne
      Shipping.belongsTo(models.Order);
    }
---
    static associate(models) {

      // hasOne
      Address.hasOne(models.User, {
        as: 'users',
        foreignKey: 'addresses_id'
      })
    }
---
- ## AHORA HAY QUE AGREGAR LAS CLAVES FORANEAS A LAS MIGRACIONES
---
migracion de Product
---
      brands_id: {
        type: Sequelize.INTEGER,
        references: {
          model: 'brands',
          key: 'id'
        }
      },
      categories_id: {
        type: Sequelize.INTEGER,
        references: {
          model: 'categories',
          key: 'id'
        }
      },
      sizes_id: {
        type: Sequelize.INTEGER,
        references: {
          model: 'sizes',
          key: 'id'
        }
      },
      genders_id: {
        type: Sequelize.INTEGER,
        references: {
          model: 'genders',
          key: 'id'
        }
      },
---
 migracion de User
---
      addresses_id: {
        type: Sequelize.INTEGER,
        references: {
          model: 'addresses',
          key: 'id'
        }
      },
---
 migracion de Image
---
      products_id: {
        type: Sequelize.INTEGER,
        references: {
          model: 'products',
          key: 'id'
        }
      },
---
 migracion de Order
---
      payments_id: {
        type: Sequelize.INTEGER,
        references: {
          model: 'payments',
          key: 'id'
        }
      },
      users_id: {
        type: Sequelize.INTEGER,
        references: {
          model: 'users',
          key: 'id'
        }
      },
      user_addresses_id: {
        type: Sequelize.INTEGER,
        references: {
          model: 'users',
          key: 'addresses_id'
        }
      },
      states_id: {
        type: Sequelize.INTEGER,
        references: {
          model: 'states',
          key: 'id'
        }
      },
---
 migracion de OrderDetail
---
      products_id: {
        type: Sequelize.INTEGER,
        references: {
          model: 'products',
          key: 'id'
        }
      },
      orders_id: {
        type: Sequelize.INTEGER,
        references: {
          model: 'orders',
          key: 'id'
        }
      },
---
 migracion de Shipping
---
      orders_id: {
        type: Sequelize.INTEGER,
        references: {
          model: 'orders',
          key: 'id'
        }
      },
---
---
- Probamos si todo va bien y ponemos por consola:
>nodemon src/app.js
- ## Ultimo pas: migrar las tablas a la base de datos:
>sequelize db:migrate
---
---
# Muestra de proyecto en Base de datos:
![Imagen del diagrama] (https://github.com/Barbara008/parcial-nodes-Panelo_2do4/blob/main/diagramaBase.jpg)