# projectmod3
#This is the project course of getting and cleaning data but i have serious problems with github
# i can connect my repository with github because of IP addres, i try to connect but at the moment to connect with the github onlyne page it saids that the connection is refused, it investigate and it is because of de IP of my computer, i try to fix it but i can´t, so i will put the sintaxis and i hope not being punish in my grade. thnks.
                                                         
                                        #PROYECTO FINAL DEL MODULO 3

#lo que estoy haciendo aqui es checar si existe un directorio y guardar la variable con el nombre del archivo.
if(!file.exists("./data")){dir.create("./data")}
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl,destfile="./data/Dataset.zip")
#Guardo el archivo para despues descomprimirlo.
#Descomprimimos el archivo con la siguiente sintaxis

unzip(zipfile="./data/Dataset.zip",exdir="./data")

#Obtener la lista de los archivos que estan dentro del folder con todo el conjunto de datos.

destino <- file.path("./data" , "UCI HAR Dataset")
archivos<-list.files(path_rf, recursive=TRUE)
archivos
#Leer los datos de los archivos en las variables.
#leer los archivos de activity
Datospruebadeentrenamiento <- read.table(file.path(destino, "test" , "Y_test.txt" ),header = FALSE)
Datosdeactividadeprueba <- read.table(file.path(destino, "train", "Y_train.txt"),header = FALSE)
#Leer los archivos de subject
datostemaentrenamiento <- read.table(file.path(destino, "train", "subject_train.txt"),header = FALSE)
datostemaprueba <- read.table(file.path(destino, "test" , "subject_test.txt"),header = FALSE)

#Leer los archivos de features
datoscaracteristicaprueba  <- read.table(file.path(destino, "test" , "X_test.txt" ),header = FALSE)
datoscaracteristicaentrenamiento <- read.table(file.path(destino, "train", "X_train.txt"),header = FALSE)
#Vemos todas las propiedades de las variables anteriores.
str(Datospruebadeentrenamiento)

str(Datosdeactividadeprueba)

str(datostemaentrenamiento)

str(datostemaprueba)

str(datoscaracteristicaprueba)

str(datoscaracteristicaentrenamiento)



#Merges the training and the test sets to create one data set
#Concatenar las tablas de datos por filas
dataSubject <- rbind(Datospruebadeentrenamiento, Datosdeactividadeprueba)
dataActivity<- rbind(datostemaentrenamiento, datostemaprueba)
dataFeatures<- rbind(datoscaracteristicaprueba, datoscaracteristicaentrenamiento)
#nombres de conjuntos de las variables
names(dataSubject)<-c("subject")
names(dataActivity)<- c("activity")
dataFeaturesNames <- read.table(file.path(destino, "features.txt"),head=FALSE)
names(dataFeatures)<- dataFeaturesNames$V2
#Combinar columnas para obtener los datos de trama de datos para todos los datos
dataCombine <- cbind(dataSubject, dataActivity)
Data <- cbind(dataFeatures, dataCombine)
#Extrae sólo las mediciones en la media y la desviación estándar para cada medición



#Pregunta 2/question 2
subdataFeaturesNames<-dataFeaturesNames$V2[grep("mean\\(\\)|std\\(\\)", dataFeaturesNames$V2)]

selectedNames<-c(as.character(subdataFeaturesNames), "subject", "activity" )
Data<-subset(Data,select=selectedNames)

str(Data)



#Utiliza nombres descriptivos de actividad a nombre de las actividades en el conjunto de datos
#Pregunta 3/ question 3
activityLabels <- read.table(file.path(path_rf, "activity_labels.txt"),header = FALSE)
head(Data$activity,30)

#Apropiadamente etiquetas conjunto de datos con los nombres de las variables descriptivas
#Pregunta 4/ question 4
names(Data)<-gsub("^t", "time", names(Data))
names(Data)<-gsub("^f", "frequency", names(Data))
names(Data)<-gsub("Acc", "Accelerometer", names(Data))
names(Data)<-gsub("Gyro", "Gyroscope", names(Data))
names(Data)<-gsub("Mag", "Magnitude", names(Data))
names(Data)<-gsub("BodyBody", "Body", names(Data))

names(Data)



#Crea un segundo, los datos ordenados independientes fijados y ouput que
#Pregunta 5/question 5.
library(plyr);
Data2<-aggregate(. ~subject + activity, Data, mean)
Data2<-Data2[order(Data2$subject,Data2$activity),]
write.table(Data2, file = "tidydata.txt",row.name=FALSE)




