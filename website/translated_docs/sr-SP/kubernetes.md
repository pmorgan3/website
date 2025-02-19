---
id: kubernetes
title: "Kubernetes"
---

 Можете наћи упутства како да извршите deploy Verdaccio-a на Kubernetes кластер у [verdaccio/docker-example](https://github.com/verdaccio/docker-examples/tree/master/kubernetes-example) репозиторијуму. Ипак, препоручујемо да инсталирате Verdaccio на Kubernetes кластер тако што ћете користити [Helm](https://helm.sh). Helm је [Kubernetes](https://kubernetes.io) package manager који доноси многе погодности.

<div id="codefund">''</div>

## Helm

### Setup Helm

Ако раније нисте користили Helm, потребно је да подесите Helm контролер звани Tiller:

```bash
helm init
```

### Инсталирање

> ⚠️ If you are using this helm chart, please [be aware of the migration of the repository](https://github.com/verdaccio/verdaccio/issues/1767).

Deploy the Helm [verdaccio/verdaccio](https://github.com/verdaccio/charts) chart.

### Add repository

    helm repo add verdaccio https://charts.verdaccio.org
    

У овом примеру користимо `npm` као име издања:

```bash
helm install --name npm verdaccio/verdaccio
```

### Постављање специфичне верзије (deploy)

```bash
helm install --name npm --set image.tag=3.13.1 verdaccio/verdaccio
```

### Надограђивање Verdaccio-а

```bash
helm upgrade npm verdaccio/verdaccio
```

### Деинсталирање

```bash
helm del --purge npm
```

**Напомена:** ова команда брише све ресурсе, укључујући и пакете који су можда раније објављени у регистрију.

### Корисничка конфигурација Verdaccio-а

Можете подесити Verdaccio конфигурацију по својим жељама тако што ћете користити Kubernetes *configMap*.

#### Припрема

Copy the [existing configuration](https://github.com/verdaccio/verdaccio/blob/master/conf/docker.yaml) and adapt it for your use case:

```bash
wget https://raw.githubusercontent.com/verdaccio/verdaccio/master/conf/docker.yaml -O config.yaml
```

**Напомена:** Проверите да ли користите исправан path за storage који се користи за persistency:

```yaml
storage: /verdaccio/storage/data
auth:
  htpasswd:
    file: /verdaccio/storage/htpasswd
```

#### Постављање configMap (deploy)

Поставите `configMap` на кластер

```bash
kubectl create configmap verdaccio-config --from-file ./config.yaml
```

#### Поставите Verdaccio

Сада можете поставити Verdaccio Helm chart и детаљно дефинисати конфигурацију која ће да се користи:

```bash
helm install --name npm --set customConfigMap=verdaccio-config verdaccio/verdaccio
```

## Rancher Support

[Rancher](http://rancher.com/) је комплетна container management платформа која Вам омогућава да на лак и једноставан начин користите контејнере.

* [verdaccio-rancher](https://github.com/lgaticaq/verdaccio-rancher)