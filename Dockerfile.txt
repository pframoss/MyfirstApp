# Utiliza una imagen base de Python 3.11
FROM python:3.11.0-slim

RUN pip install pip==24.0
RUN pip install setuptools==69.0.3
 
# Instala paquete para oracle client
RUN apt-get update && apt-get install -y libaio1 

# Ruta de instalación del cliente de oracle
RUN mkdir -p /opt/oracle

# Establece el directorio de trabajo dentro del contenedor
WORKDIR /app

# Copia el archivo de requerimientos al contenedor
COPY requirements.txt .

# Instala las dependencias
RUN pip install --no-cache-dir -r requirements.txt

# Copia el código fuente de la aplicación al contenedor
COPY . .

# Configura el cliente de oracle
RUN unzip /app/src/app/opt/instantclient-basic-linux.x64-11.2.0.4.0.zip -d /opt/oracle

RUN sh -c "echo /opt/oracle/instantclient_11_2 > /etc/ld.so.conf.d/oracle-instantclient.conf"
RUN ldconfig
RUN export LD_LIBRARY_PATH=/opt/oracle/instantclient_11_2:$LD_LIBRARY_PATH

# Eposición del puerto donde se ejecuta el app
EXPOSE 8501

# Comando para inicializar el app
CMD ["streamlit", "run", "src/app/app.py"]
