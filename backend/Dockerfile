FROM python:3.10-slim as python-base

ARG YOUR_ENV=production

ENV YOUR_ENV=${YOUR_ENV} \
    PYTHONFAULTHANDLER=1 \
    PYTHONUNBUFFERED=1 \
    # prevents python creating .pyc files
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONHASHSEED=random \
    # pip
    PIP_NO_CACHE_DIR=off \
    PIP_DISABLE_PIP_VERSION_CHECK=on \
    PIP_DEFAULT_TIMEOUT=100 \
    # poetry
    POETRY_VERSION=1.2.0b1 \
    # make poetry install to this location
    POETRY_HOME="/opt/poetry" \
    # make poetry create the virtual environment in the project's root
    # it gets named `.venv`
    POETRY_VIRTUALENVS_IN_PROJECT=true \
    # do not ask any interactive question
    POETRY_NO_INTERACTION=1 \
    # paths
    # this is where our requirements + virtual environment will live
    PYSETUP_PATH="/opt/pysetup" \
    VENV_PATH="/opt/pysetup/.venv"

# prepend poetry and venv to path
ENV PATH="$POETRY_HOME/bin:$VENV_PATH/bin:$PATH"

# install system dependencies and setup
# `builder-base` stage is used to build deps + create our virtual environment
FROM python-base as builder-base
RUN apt-get update \
    && apt-get install --no-install-recommends -y \
    # deps for installing poetry
    curl locales \
    # deps for building python deps
    build-essential

RUN sed -i -e 's/# es_GT.UTF-8 UTF-8/es_GT.UTF-8 UTF-8/' /etc/locale.gen \
    && locale-gen

ENV LANG es_GT.UTF-8
ENV LC_ALL es_GT.UTF-8

# install poetry - respects $POETRY_VERSION & $POETRY_HOME
RUN curl -sSL https://install.python-poetry.org | python3 -

# copy project requirement files here to ensure they will be cached.
WORKDIR $PYSETUP_PATH

COPY . .

# install project dependencies
RUN poetry install $(test "$YOUR_ENV" = production && echo "--default") --no-ansi


# `production` image used for runtime
FROM python-base as production

WORKDIR $PYSETUP_PATH

# copy in our venv
COPY --from=builder-base $PYSETUP_PATH $PYSETUP_PATH

# set the work directory
WORKDIR /app

COPY . .

CMD uvicorn backend.main:app --host=0.0.0.0 