# Tarea 05
### Emanuel O. Bassoco (CU: 130669)

#### Definición de archivos DockerFile:

* Archivo para Docker de entrenamiento:
```plaintext

## Imagen base de miniconda
FROM continuumio/miniconda3

## Define working directory dentro del contendor
WORKDIR /home

## Copia scripts locales al contenedor
COPY src/train.py .
COPY data/prep.csv .
COPY enviroment.yml

## Levanta ambiente conda en el contenedor
RUN conda env create -f enviroment.yml

## Define path para ejecucion de ambiente
ENV PATH /opt/conda/envs/sales_prediction/bin:$PATH

## Ejecución de script
ENTRYPOINT ["python", "train.py"]
```

* Archivo para Docker de inferencia
```plaintext

## Imagen base de miniconda
FROM continuumio/miniconda3

## Define working directory dentro del contendor
WORKDIR /home

## Copia scripts locales al contenedor
COPY src/inference.py .
COPY model ./model
COPY enviroment.yml

## Levanta ambiente conda en el contenedor
RUN conda env create -f enviroment.yml

## Define path para ejecucion de ambiente
ENV PATH /opt/conda/envs/sales_prediction/bin:$PATH

## Ejecución de script
ENTRYPOINT ["python", "inference.py"]

```

* Archivo YAML para ambientes conda de los contenedores:
```plaintext
name: sales_prediction
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.11
  - pandas=2.2.2  
  - statsforecast=2.0.1
  - hierarchicalforecast=1.0.1
```

#### Comandos en SH para construir contendores y ejecutar scripts

* Para levantar contendores (ejecutar donde se tienen los Dockerfile):
```sh
docker build -t sales_prediction_train -f Dockerfile.train .
```

```sh
docker build -t sales_prediction_inference -f Dockerfile.inference .
```

* Para ejecutar los scripts:
```sh
docker run --rm -t -v $path_local/model:/home/models sales_prediction_train --input_file prep.csv --output_path models  --season_length 1
```
```sh
docker run --rm -t -v $path_local/data:/home/data sales_prediction_inference --path_insumo model --output_path data --h_pron 30
```


