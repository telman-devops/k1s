## Что это?
Набор скриптов для того, чтобы быстро поднять простенький кластер Kubernetes с сетью на Flannel.

Окружение:
* виртуальная машина (VirtualBox, но сойдет любой способ)
* Ubuntu 18.04

У каждой виртуальной машины есть два сетевых адаптера
* NAT (для доступа в Internet)
* Host-only (для общения с другими машинами и нашей настоящей)

## Создание кластера

1. Устанавливаем необходимые пакеты и закрепляем их версии

`sudo ./install`

2. Редактируем init, чтобы указать там, какой IP у нашего Host-only адаптера.

3. Создаем кластер

`sudo ./init`

Результатом работы init будет строка с командой `kubeadm join ...`, которую нужно сохранить, если мы будем использовать более 1 ноды

4. Копируем файл конфигурации для kubectl

```
$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

5. Настраиваем сеть. **Внимание! В файле kube-flannel.yml указан интерфейс enp0s8.** Если в виртуальной машине он называется по-другому (допустим, enp1s2), то нужно выполнить команду

`sed -i s/enp0s8/enp1s2/g kube-flannel.yml`

6. Устанавливаем Flannel

`kubectl -f apply kube-flannel.yml`

## Присоединение ноды

1. Устанавливаем необходимые пакеты и закрепляем их версии

`sudo ./install`

2. Редактируем файл join, чтобы:
- указать там, какой IP у нашего Host-only адаптера.
- вставить туда строку `kubeadm join ...` из создания кластера

3. Выполняем присоединение

`sudo ./join`

