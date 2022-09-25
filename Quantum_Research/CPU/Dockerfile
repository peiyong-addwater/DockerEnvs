# syntax=docker/dockerfile:1
# FROM python:3.8-slim-buster
# FROM allennlp/allennlp:latest
# FROM ubuntu:18.04
# ENV PATH="/root/miniconda3/bin:${PATH}"
# ARG PATH="/root/miniconda3/bin:${PATH}"
FROM continuumio/anaconda3
# Potentially fix issue where /bin/sh cannot be openned
SHELL ["/bin/bash", "-c"]
# create a working directory
WORKDIR /app
RUN apt-get update
RUN apt-get -y install build-essential
#RUN wget \
#    https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
#    && mkdir /root/.conda \
#    && bash Miniconda3-latest-Linux-x86_64.sh -b \
#    && rm -f Miniconda3-latest-Linux-x86_64.sh 
# copy the local requirements file to the image
COPY ./requirements.txt /requirements.txt
# install the python dependencies
RUN pip install -U pip pip-tools && pip install --no-cache-dir cython
RUN pip install -U pip pip-tools && pip install --no-cache-dir numpy
RUN pip install --upgrade pip
RUN pip install -U pip pip-tools && pip install --no-cache-dir -r /requirements.txt && rm -rf /requirements.txt
# RUN pip install -U allennlp allennlp-models
RUN pip install cached-path==1.1.2
RUN pip install --upgrade google-cloud-storage
# COPY ./download_depccg_model.py /download_depccg_model.py
# RUN python /download_depccg_model.py
# RUN pip install -U depccg
# RUN depccg_en download
# RUN depccg_en download elmo
RUN apt-get update && \
    apt-get install --no-install-recommends -y \
        biber \
        latexmk\
        texlive-latex-extra && \
        rm -rf /var/lib/apt/lists/*

ENV JUPYTER_ENABLE_LAB=yes