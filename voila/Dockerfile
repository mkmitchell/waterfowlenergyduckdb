# Base image with Jupyter and Python
FROM jupyter/base-notebook:python-3.11

# Set working directory
WORKDIR /home/jovyan/app

# Copy your notebook and supporting files into the image
COPY . /home/jovyan/app

# Install system packages (optional, if needed by geopandas, etc.)
USER root
RUN apt-get update && \
    apt-get install -y \
    gdal-bin \
    libgdal-dev \
    libspatialindex-dev \
    && rm -rf /var/lib/apt/lists/*
USER jovyan

# Install required Python packages
COPY requirements.txt requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Expose Voila on port 8888
EXPOSE 9000

# Run Voila directly
#CMD ["voila", "--port=9000", "--no-browser", "--Voila.configuration_allow_origin='https://ducksgis.org'", "--Voila.show_tracebacks=True", "--Voila.enable_nbextensions=True","WaterfowlEnergyDuckdb_voila.ipynb"]
CMD ["voila", "--port=9000", "--no-browser", "--allow-origin=https://ducksgis.org", "--show-tracebacks", "--enable_nbextensions=True", "WaterfowlEnergyDuckdb_voila.ipynb"]

