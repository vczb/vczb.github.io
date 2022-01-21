---
layout: post
title:  "Configurando notificações de bateria no Ubuntu 20.04"
date:   2022-01-21 19:00:00 +0530
categories: linux ubuntu
---


Sempre achei o percentual para notificar carga baixa da bateria no Ubuntu 20.04 ruim, o sistema esperava até os últimos suspiros para me avisar “Bateria baixa”. Em alguns casos nem dava tempo de pegar o carregador e colocá-lo na tomada, o note apagava.

A primeira solução que me veio à cabeça foi criar um [script](https://gist.github.com/vczb/c54edcf5ba542b9b0699e505620b4767) que media a bateria a cada x tempo e notifica se for necessário

```
#!/bin/bash

while sleep 180s

do
 STATE=$(upower -i /org/freedesktop/UPower/devices/battery_BAT0| grep -E "state" | sed  -e 's/state://ig');

 PERCENT=$(upower -i /org/freedesktop/UPower/devices/battery_BAT0| grep -E "percentage" | sed  -e 's/[^0-9]//ig')

 if [ $STATE != 'charging' ]; then

   if [ $PERCENT -lt 20 ]; then

     notify-send -i battery-caution "Battery with only $PERCENT%" '\nConnect your notebook to the charger\n'

   fi

 else

   if [ $PERCENT -gt 92 ]; then

     notify-send -i battery-full "Battery charged successfully" '\nDisconnect your notebook from the charger\n'

   fi

 fi

done
```
E funcionou, sem problema nenhum… mas eu queria encontrar uma solução melhor, que não fosse necessário executar nada em segundo plano.

Foi então que após algumas horas de pesquisa descobri o arquivo [UPower.conf](https://gist.github.com/vczb/4ffcdfd39d87001aab249a87ca72fcbe) localizado no path `/etc/UPower/Upower.conf`

Nele estão as configurações de energia do sistema. Por default as configurações de notificação são:


```
//linha 65
PercentageLow=10
PercentageCritical=3
PercentageAction=2
```

Após uma simples alteração nos valores meu problema foi solucionado:


```
PercentageLow=30
PercentageCritical=10
PercentageAction=5
```

Agora sim, logo que chegar aos 30% de carga recebo uma notificação do sistema. =)

---

Espero que este tutorial tenha te ajudado



