FROM jupyter/base-notebook:latest

USER root

COPY jupyter/install_cuda.sh /usr/bin/

RUN /bin/bash /usr/bin/install_cuda.sh

# Define environment variables
ENV SPARK_VERSION=3.3.2
ENV HADOOP_MAJOR_VERSION=3
ENV HADOOP_MINOR_VERSION=3.4
ENV HADOO_VERSION=${HADOO_MAJOR_VERSION}.${HADOOP_MINOR_VERSION}
ENV SCALA_VERSION=2.13
ENV DELTA_LAKE_VERSION=2.3.0
ENV HADOOP_AWS_VERSION=3.3.4

ENV HADOOP_HOME=/usr/hadoop-${HADOOP_VERSION}
ENV SPARK_PACKAGE=spark-${SPARK_VERSION}-bin-hadoop${HADOOP_MAJOR_VERSION}-scala${SCALA_VERSION}
ENV SPARK_HOME=/usr/${SPARK_PACKAGE}

ENV PATH $PATH:${SPARK_HOME}/bin

# Install necessary packages
RUN apt-get update && \
    apt-get install -y wget vim openjdk-11-jdk

# Download and install Spark
RUN wget -O /tmp/${SPARK_PACKAGE}.tgz https://archive.apache.org/dist/spark/spark-${SPARK_VERSION}/${SPARK_PACKAGE}.tgz && \
    tar -xvf /tmp/${SPARK_PACKAGE}.tgz -C /usr/ && \
    rm /tmp/${SPARK_PACKAGE}.tgz

# Download and copy Delta Lake JAR file to the Spark jars directory
RUN wget -O /tmp/delta-lake-${DELTA_LAKE_VERSION}.jar https://repo1.maven.org/maven2/io/delta/delta-core_${SCALA_VERSION}/${DELTA_LAKE_VERSION}/delta-core_${SCALA_VERSION}-${DELTA_LAKE_VERSION}.jar && \
    mv /tmp/delta-lake-${DELTA_LAKE_VERSION}.jar ${SPARK_HOME}/jars
    
# Download and copy Delta Lake JAR file to the Spark jars directory
RUN wget -O /tmp/delta-contribs-${DELTA_LAKE_VERSION}.jar https://repo1.maven.org/maven2/io/delta/delta-contribs_${SCALA_VERSION}/${DELTA_LAKE_VERSION}/delta-contribs_${SCALA_VERSION}-${DELTA_LAKE_VERSION}.jar && \
    mv /tmp/delta-contribs-${DELTA_LAKE_VERSION}.jar ${SPARK_HOME}/jars
    
# Download and copy Delta Lake JAR file to the Spark jars directory
RUN wget -O /tmp/delta-storage-${DELTA_LAKE_VERSION}.jar https://repo1.maven.org/maven2/io/delta/delta-storage-s3-dynamodb/${DELTA_LAKE_VERSION}/delta-storage-s3-dynamodb-${DELTA_LAKE_VERSION}.jar && \
    mv /tmp/delta-storage-${DELTA_LAKE_VERSION}.jar ${SPARK_HOME}/jars

# Download and copy AWS SDK and Hadoop AWS JAR files to the Spark jars directory
RUN wget https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-aws/${HADOOP_AWS_VERSION}/hadoop-aws-${HADOOP_AWS_VERSION}.jar -P ${SPARK_HOME}/jars/ && \
    wget https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk-bundle/1.12.469/aws-java-sdk-bundle-1.12.469.jar -P ${SPARK_HOME}/jars/

RUN apt-get update && apt install -y python3 python3-pip &&  pip3 install --upgrade pip setuptools

RUN pip3 install pyspark==${SPARK_VERSION}

RUN pip3 install delta-spark==${DELTA_LAKE_VERSION}

RUN echo "spark.jar.packages io.delta:delta-core_${SCALA_VERSION}:${DELTA_VERSION},org.apache.hadoop:hadoop-aws:${HADOOP_VERSION}" > $SPARK_HOME/conf/spark-defaults.conf

RUN chown -R ${NB_UID}:users ${SPARK_HOME}

USER ${NB_UID}
