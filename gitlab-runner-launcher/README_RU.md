## Prerequisites
- linux-система на x86_64 (гарантированно работает на Ubuntu 20.04). Работа на MacOS и/или других архитектурах не гарантируется.
- [Rootless docker](https://docs.docker.com/engine/security/rootless/).
  Для обычного (rootful) докера нужно переопределить `DOCKER_HOST`, возможно, понадобятся дополнительные модификации скрипта.
- (Опционально) `jq`. Требуется только для автоматического добавления prune-фильтра при использовании кеша DinD.

## Installation
Достаточно скопировать [girlab-runner](https://raw.githubusercontent.com/vvd170501/cli-utils/master/gitlab-runner-launcher/gitlab-runner) в любую директорию, входящую в `PATH`.

## Usage
Получение токена: в репозитории, к которому подключается раннер, открыть `Settings -> CI/CD -> Runners`, скопировать токен.
Там же можно отключить использование shared-раннеров (по умолчанию они продолжают использоваться).

[Как использовать раннер для несколькиз репозиториев](https://gitlab.com/gitlab-org/gitlab-runner/-/issues/1781#note_51979406)
```bash
# регистрация
gitlab-runner register
# запуск
gitlab-runner run
# остановить и удалить раннер (можно запустить заново командой run, повторная регистрация не нужна)
gitlab-runner stop && gitlab-runner rm
# отменить регистрацию
gitlab-runner unregister
# также доступны другие команды. Вывести полный список - "gitlab-runner --help"

# Раннер с кешем для DinD (ускоряет сборку/запуск контейнеров, но могут возникнуть неочевидные проблемы)
# Параллельно может выполняться только одна задача из CI.
# При необходимости можно создать несколько раннеров (у каждого будет свой кеш, поэтому увеличится использование диска)
export USE_DIND_CACHE=1 RUNNER_NAME=runner-with-dind-cache  # переменные нужны не только на этапе регистрации
gitlab-runner register
gitlab-runner run
# очистить кеш DinD (на время очистки раннер останавливается)
gitlab-runner dind-prune
# Полностью очистить кеш (включая образы и docker volumes)
gitlab-runner dind-prune --volumes -a
```
