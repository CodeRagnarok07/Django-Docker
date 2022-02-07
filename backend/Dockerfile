# For more information, please refer to https://aka.ms/vscode-docker-python
FROM python:3.9

EXPOSE 8000

# Keeps Python from generating .pyc files in the container
ENV PYTHONDONTWRITEBYTECODE=1

# Turns off buffering for easier container logging
ENV PYTHONUNBUFFERED=1

# Install pip requirements 
# COPY .requirements.txt /code/
# RUN python -m pip install -r requirements.txt

WORKDIR /backend
COPY . /backend/

# Creates a non-root user with an explicit UID and adds permission to access the /app folder
# For more info, please refer to https://aka.ms/vscode-docker-python-configure-containers
# RUN adduser -u 5678 --disabled-password --gecos "" appuser && chown -R appuser /app
# USER appuser

# Configure apt and install packages
# RUN apt-get update \
#     && apt-get -y install --no-install-recommends apt-utils dialog 2>&1 \
#     #
#     # Verify git, process tools, lsb-release (common in install instructions for CLIs) installed
#     # && apt-get -y install git iproute2 procps lsb-release \
#     #
#     # Install pylint
#     #&& pip install pylint \
#     #
#     # Update Python environment based on requirements.txt
#     # && pip --disable-pip-version-check --no-cache-dir install -r /tmp/pip-tmp/requirements.txt \
#     # && rm -rf /tmp/pip-tmp \
#     #
#     # Install node for building front-ends
#     # && apt-get update \
#     # && apt-get -y install curl gnupg \
#     # && curl -sL https://deb.nodesource.com/setup_11.x  | bash - \
#     # && apt-get -y install nodejs \
#     #
#     # Create a non-root user to use if preferred - see https://aka.ms/vscode-remote/containers/non-root-user.
#     && groupadd --gid $USER_GID $USERNAME \
#     && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
#     # [Optional] Add sudo support for the non-root user
#     && apt-get install -y sudo \
#     && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME\
#     && chmod 0440 /etc/sudoers.d/$USERNAME \
#     #
#     # Clean up
#     && apt-get autoremove -y \
#     && apt-get clean -y \
#     && rm -rf /var/lib/apt/lists/*

# During debugging, this entry point will be overridden. For more information, please refer to https://aka.ms/vscode-docker-python-debug
# File wsgi.py was not found in subfolder: 'dockerdj'. Please enter the Python path to wsgi file.
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "pythonPath.to.wsgi"]
