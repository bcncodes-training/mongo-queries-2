## Mongo-queries-2
Crear una bd “libreria” y cargar los scripts adjuntos con el comando `load("nombrefichero")` desde el __mongo shell__.

Lanzar las siguientes queries:

- Obtener todos los autores

      db.libros.aggregate([
        {$group:{_id:{"autor":"$autor.apellidos","nombre":"$autor.nombre"}}}
      ])

- Obtener los autores cuyo apellido sea DATE

      db.libros.aggregate([
        {$match:{"autor.apellidos":{$in:["DATE"]}}},
        {$group:{_id:{"autor":"$autor.apellidos","nombre":"$autor.nombre"}}}
      ])

- Obtener los libros editados en 1998 o en 2005

      db.libros.aggregate([
        {$match:
            {"anyo":
                  {$in:["2005","1998"]}
            }
        }
      ])

- Obtener el número de libros de la editorial Addison‐Wesley

      db.libros.aggregate([
        {$match:{"editorial":{$in:["Addison-Wesley"]}}} 
      ])
      
- Obtener el libro que ocupa la tercera posición

      db.libros.find().skip(2).limit(1).pretty()

- Obtener la lista de autores de cada libro junto con el título

      db.libros.aggregate([
        {$project:
          {
            _id:0,
            name:"$autor", 
            libro:"$titulo"
          }
        }
      ])
      
- Obtener los títulos de libro publicados con posterioridad a 2004.

      db.libros.aggregate([
        {
           $addFields: {
              yearInt: { $toInt: "$anyo" }
           }
        },
        {$match:{
          yearInt:{$gt:2004}
          }
        }
      ])
      
- Obtener los libros editados desde 2001 y precio mayor que 50

      db.libros.aggregate([
        {
           $addFields: {
              yearInt: { $toInt: "$anyo" }
           }
        },
        {
          $match:
            {$and:[
              {yearInt:{$gt:2001}},
              {precio:{$gt:50}}
            ]
          }
        }
      ])
      
- Obtener los libros publicados por la editorial Addison‐Wesley después de 2005.

      db.libros.aggregate([
        {
           $addFields: {
              yearInt: { $toInt: "$anyo" }
           }
        },
        {
          $match:
            {$and:[
              {yearInt:{$gt:2005}},
              {editorial:{$in:["Addison-Wesley"]}}
            ]
          }
        }
      ])
      
- Obtener el título de libro y editorial para aquellos libros que tengan un precio superior a 50€.

      db.libros.aggregate([
        {$match:{
          precio:{$gt:50}
          }
        },
        {$project:
          {
            _id:0,
            libro:"$titulo",
            editorial:"$editorial", 
          }
        }
      ])
      
- Obtener los libros publicados en 1998 o a partir de 2005.

      db.libros.aggregate([
        {
           $addFields: {
              yearInt: { $toInt: "$anyo" }
           }
        },
        {
          $match:{
            $or:[
              {yearInt:{$gt:2005}},
              {anyo:{$in:["1998"]}}
            ]
          }
        }
      ])
