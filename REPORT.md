Preguntas Reporte Tecnico

// Cual es el bug raiz y en que archivo/funcion esta?
El bug principal estaba en el archivo crypto/algif_aead.c dentro de la funcion _aead_recvmsg() ahi el kernel hacia una escritura en una posicion incorrecta usando dst[assoclen + cryptlen] y eso provocaba que se escribieran datos fuera del espacio esperado al inicio parecia un error pequeno pero despues entendi que eso permitia modificar partes sensibles de la memoria y por eso se podia obtener acceso root dentro de la maquina virtual

// Por que el write a dst[assoclen + cryptlen] es peligroso?
Es peligroso porque el kernel puede terminar escribiendo informacion en lugares donde no deberia eso significa que un usuario normal puede alterar memoria importante aunque no tenga permisos para hacerlo normalmente el sistema operativo deberia controlar exactamente que zonas de memoria se pueden modificar pero por este error se rompia esa proteccion y el exploit aprovechaba eso para cambiar datos importantes y escalar privilegios

// Por que el exploit es "stealthy" (no modifica el archivo en disco)?
El exploit es stealthy porque realmente no cambia el archivo original en disco sino que modifica la informacion que esta cargada en memoria usando la page cache entonces el archivo parece normal si alguien lo revisa pero en memoria si hay cambios temporales eso hace que sea mas dificil detectar el ataque porque casi no deja rastros visibles y el binario original sigue viendose igual aunque el proceso ya este alterado

// Conecta esto con lo que vimos en clase: page cache chmod setuid inodos
En este laboratorio pude entender mejor como funciona la page cache porque el exploit aprovechaba datos que estaban cargados en memoria y no directamente en el disco tambien se relaciona con setuid porque el archivo explotado podia ejecutarse con privilegios elevados aunque el usuario fuera student ademas chmod sirve para controlar permisos pero en este caso el problema venia desde el kernel entonces los permisos normales no bastaban para detenerlo y sobre los inodos entendi que representan el archivo real mientras que la page cache guarda copias temporales en memoria y ahi era donde ocurria la modificacion

// Que aprendiste sobre como multiples cambios "razonables" pueden crear un bug grave?
Aprendi que varios cambios pequenos que parecen normales pueden terminar causando un problema muy serio probablemente cada cambio por separado tenia sentido pero al combinar manejo de memoria optimizaciones y funciones criptograficas aparecio una vulnerabilidad muy peligrosa esto me hizo entender que en sistemas operativos cualquier detalle importa mucho porque un error pequeno en codigo de bajo nivel puede terminar comprometiendo toda la seguridad del sistema y permitir que un usuario normal consiga acceso root