

Procesamiento
-------------

### Construccion de CM_ENTREGABLE : "update_CM_ENTREGABLE"
* Datos base
	* CargaMasivaVoluntarios_V2.xlsx 	-> ges_planillas_fcr
	* INSTITUCIONES.xls 				-> INSTITUCIONES
	* POMELO/RCO_CENSO2017 varias tablas
	
* Output
	* CM_ENTREGABLE

* Proceso
	* Copia de tablas desdde POMELO
		* "glo_nivel","glo_contrato","glo_sede","geo_comuna","geo_provincia","geo_region","glo_perfil"
			,"glo_sexo","glo_origen","glo_proceso","glo_establecimiento","glo_tipo_establecimiento"
			,"glo_dependencia_institucional","ges_usuario","ges_voluntario","log_carga_masiva"
	* exec dbo.[1_Limpiar_Rut]
	* Actualizar SGC
	* exec dbo.[2_Cruce_y_duplicados]
	* exec dbo.[3_Entregable_CM]

	
### Edición de CM_ENTREGABLE : "edit_CM_ENTREGABLE"
* Datos base
	* CM_ENTREGABLE
	* Configs:
	
* Output
	* CM_PUBLISH
	
* Proceso
	* Regularizar Sexo
	* Regularizar Fecha de Nacimiento
	* Calcular Edad al dia del Censo
	* Regularizar perfil	
	* Regularizar nivel
	* Regularizar tipo_institucion
	* Regularizar dependencia_institucion
	* Regularizar tipo_contrato
	* Regularizar comuna_voluntario
	* Regularizar comuna_sede
	* Regularizar ministerio
	* Tipo.Inst. segun Nombre.Inst. (ByNomInst.TipoInst)
	* Ministerio según Nombre.Inst. (ByNomInst.Ministerio)
	* Corregir Nom.Inst.Publica por diccionario
	* Separar dataset por tipo_institucion
		* "Sector Público":
			* Construir diccionario exchaustivo de nombres de instituciones
			* Reemplazar nombre_institucion y ministerio por diccionario
		* "Municipalidad": 
			* Construir diccionario pares de nombres de instituciones y sede
			* calcular div_municipal
		* otros
			* nada
	* Re-unir dataset
	* Adicionar códigos de region, provincia y comuna para comuna propuesta
	* Adicionar campo clase contrato
		
	
### Regularización de texto : Capitalize()
* Elimina espacios delante y detrás de texto
* Elimina dobles espacios y espacios delante de comas
* Asigna mayúsculas y minúsculas

### Aplanamiento de texto : FlatText()
* En general se usa despues de Capitalize()
* pasa todo a minúsculas
* elimina tildes


### Regularización simple
* Aplica a:
 	* Sexo, Fecha de Nacimiento, perfil	, nivel, tipo_institucion, dependencia_institucion,
		tipo_contrato, comuna_voluntario, comuna_sede, ministerio
* Proceso
	* Capitalize()
	* Si está entre valores válidos, -> se preserva
		si no                        -> "_No_Valido_"
		
	
		
		

	
Tablas
-------

### ALT_NOMBRE_MIN
Nombres alternativos de ministerios

* glosa : glosa alternativa
* Cod   : código en tabla DIC_MINISTERIO

No se está usando por ahora.

### ALT_NOM_INST_PUB
Nombres alternativos de instituciones Sector Público

* nombre_institucion : glosa alternativa
* cod : código en tabla DIC_INST_PUBLICA

### CM_DECAF
versión de CM_PUBLISH sin datos personales

### CM_ENTREGABLE
tabla intermedia, resultado de procesamiento armado por Aris

### CM_PUBLISH
tabla que se publica.

La versión de producción queda en ARTEMISA, 
servidor PABLO_LEON contiene versión de desarrollo

### CM_PUBLISH_LOG
log de publicación de CM_PUBLISH

### CM_TEMP
tabla intermedia, dentro de procesamiento Aris.


### DDDD_tipo_institucion
tabla "Donde Dice, Debe Decir" 

corrige tipo_institucion y/o ministerio en funcion de nombre_intitucion.

(va a requerir reeestructuración como parte del reporte por municipalidades y sect.publico)


### DIC_INST_PUBLICA
Diccionario instituciones públicas (DIPRES)

### DIC_MINISTERIO
Diccionario ministerios (relacionado con DIC_INST_PUBLICA)

### INSTITUCIONES
Tabla de instituciones provista por Aris 
Entiendo que es del sistema, pero no está entre las tablas de POMELO/RCO_CENSO2017

### SGC
Tabla intermedia de procesamiento Aris, 
registros provenientes del sistema,

### Tablas copiadas desde POMELO/RCO_CENSO2017
Estas tablas se copian desde POMELO en cada actualización de CM_PUBLISH
(eso es más barato que intentar consulta remota)

* geo_comuna
* geo_provincia
* geo_region
* ges_planillas_fcr
* ges_usuario
* ges_voluntario
* glo_contrato
* glo_dependencia_institucional
* glo_establecimiento
* glo_nivel
* glo_origen
* glo_perfil
* glo_proceso
* glo_sede
* glo_sexo
* glo_tipo_establecimiento
