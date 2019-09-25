## Mongo-queries-2
Crear una bd “libreria” y cargar los scripts adjuntos con el comando `load("nombrefichero")` desde el __mongo shell__.

Lanzar las siguientes queries:

- Obtener todos los autores

      db.libros.aggregate([
        { $unwind : "$autor" },
        { $group:{_id:{"autor":"$autor.apellidos","nombre":"$autor.nombre"}} }
      ])
      
      db.libros.aggregate(
          {$unwind : "$autor"},   
          {$group: {
              _id : {"autor":{ $concat: [ "$autor.apellidos", ", ", "$autor.nombre" ] }}
          }},
          {$group: {
              _id : "count",
              total : {"$sum" : 1},
              distinctValues : {$addToSet : "$_id"}
          }}
      )

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
      
      db.libros.aggregate(
          {$unwind : "$autor"},   
          {$group: {
              _id : {"titulo":{ $concat: [ "$autor.apellidos", ", ", "$autor.nombre", " - ", "$titulo" ] }}
          }},
          {$group: {
              _id : "count",
              total : {"$sum" : 1},
              distinctValues : {$addToSet : "$_id"}
          }}
      );
      
- Obtener los títulos de libro publicados con posterioridad a 2004.

      db.libros.find({anyo:{$gt:"2004"}},{_id:0,titulo:1}).pretty()

      db.libros.aggregate([
        {
           $addFields: {
              yearInt: { $toInt: "$anyo" }
           }
        },
        {$match:{
          yearInt:{$gt:2004}
          }
        },
	{$project:
          {
            _id:0,
            libro:"$titulo"
          }
        }
      ])
      
- Obtener los libros editados desde 2001 y precio mayor que 50

      db.libros.find({"precio":{$gt:50}},{"titulo":1,"editorial":1,"_id":0})

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
    
- Obtener por cada libro el titulo y el numero de ediciones

      db.libros.aggregate([
        {$group:{_id:"$titulo",num_ediciones:{$sum:1}}},
        {$match:{ num_ediciones: { $not:{$eq:1} }}}
      ])
      
  pasandolo por funcion de `javascript`
  
      let libros=db.libros.aggregate([
        {$group:{_id:"$titulo",num_ediciones:{$sum:1}}},
        {$match:{ num_ediciones: { $gt:1 }}}
      ])     
      libros.forEach((miDoc)=>{print(`titulo:${miDoc._id}| num. ediciones:${miDoc.num_ediciones}`)})
