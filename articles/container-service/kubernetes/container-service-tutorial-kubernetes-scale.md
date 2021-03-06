---
title: (DEPRECATO) Esercitazione per il servizio Azure Container - Ridimensionare un'applicazione
description: Esercitazione per il servizio Azure Container - Scalare un'applicazione
author: dlepow
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 2ea8a5428c1fabdfda4f2298c0559792537df481
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273993"
---
# <a name="deprecated-scale-kubernetes-pods-and-kubernetes-infrastructure"></a>(DEPRECATO) Ridimensionare i pod e l'infrastruttura Kubernetes

> [!TIP]
> Per la versione aggiornata di questa esercitazione che usa il servizio Azure Kubernetes, vedere [Esercitazione: Ridimensionare le applicazioni nel servizio Azure Kubernetes ](../../aks/tutorial-kubernetes-scale.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Se sono state eseguite le esercitazioni, si dispone di un cluster Kubernetes in funzione nel servizio Azure Container ed è stata distribuita l'app Azure Voting. 

In questa esercitazione, la quinta di sette, si aumenterà il numero di istanze dei pod nell'app e si proverà la scalabilità automatica dei pod. Si apprenderà anche come ridimensionare il numero di nodi agente delle VM di Azure per modificare la capacità del cluster per l'hosting dei carichi di lavoro. Le attività completate comprendono:

> [!div class="checklist"]
> * Scalabilità manuale di pod Kubernetes
> * Configurazione della scalabilità automatica di pod che eseguono il front-end dell'app
> * Scalare i nodi agente Azure in Kubernetes

Nelle esercitazioni successive l'applicazione Azure Vote viene aggiornata e Log Analytics viene configurato per monitorare il cluster Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore, caricata poi in Registro Azure Container, ed è stato creato un cluster Kubernetes. L'applicazione è stata quindi eseguita nel cluster Kubernetes. 

Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Tutorial 1 – Create container images](./container-service-tutorial-kubernetes-prepare-app.md) (Esercitazione 1: Creare immagini del contenitore). 

## <a name="manually-scale-pods"></a>Scalare manualmente i pod

Fino a questo momento sono stati distribuiti il front-end di Azure Vote e l'istanza di Redis, ognuno con una replica singola. Per verificare, eseguire il comando [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get).

Passare a [https://shell.azure.com](https://shell.azure.com) per aprire Cloud Shell nel browser.

```console
kubectl get pods
```

Output:

```output
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Modificare manualmente il numero di pod nella distribuzione `azure-vote-front` usando il comando [kubectl scale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale). In questo esempio il numero aumenta a 5.

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Eseguire [kubectl get pods](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) per verificare che Kubernetes stia creando i pod. Dopo circa un minuto i pod aggiuntivi sono in esecuzione:

```console
kubectl get pods
```

Output:

```output
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Scalare automaticamente i pod

Kubernetes supporta la [scalabilità automatica orizzontale dei pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) per modificare il numero dei pod in una distribuzione a seconda dell'utilizzo della CPU o delle altre metriche selezionate. 

Per usare la scalabilità automatica, i pod devono avere richieste e limiti di CPU definiti. Nella distribuzione di `azure-vote-front` il contenitore front-end richiede un valore di 0,25 della CPU, con un limite pari a 0,5 della CPU. Le impostazioni sono simili:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

L'esempio seguente usa il comando [kubectl autoscale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale) per scalare automaticamente il numero di pod nella distribuzione `azure-vote-front`. In questo caso, se l'utilizzo della CPU supera il 50%, la scalabilità automatica aumenta il numero di pod fino al valore massimo di 10.


```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Per visualizzare lo stato della scalabilità automatica, eseguire il comando seguente:

```console
kubectl get hpa
```

Output:

```output
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Dopo pochi minuti con un carico minimo sull'app Azure Vote, il numero di repliche di pod si riduce automaticamente a 3.

## <a name="scale-the-agents"></a>Scalare gli agenti

Se è stato creato usando i comandi predefiniti nell'esercitazione precedente, il cluster Kubernetes dispone di tre nodi agente. Se si prevede un numero maggiore o minore di carichi di lavoro dei contenitori nel cluster, è possibile modificare manualmente il numero di agenti. Usare il comando [az acs scale](/cli/azure/acs#az-acs-scale) e specificare il numero di agenti con il parametro `--new-agent-count`.

Nell'esempio seguente il numero di nodi agente viene aumentato a 4 nel cluster Kubernetes, denominato *myK8sCluster*. Il completamento del comando richiede alcuni minuti.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

L'output del comando mostra il numero di nodi agente nel valore di `agentPoolProfiles:count`:

```output
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state usate diverse funzionalità di scalabilità nel cluster Kubernetes. Le attività descritte includevano:

> [!div class="checklist"]
> * Scalabilità manuale di pod Kubernetes
> * Configurazione della scalabilità automatica di pod che eseguono il front-end dell'app
> * Scalare i nodi agente Azure in Kubernetes

Passare all'esercitazione successiva per apprendere come aggiornare l'applicazione in Kubernetes.

> [!div class="nextstepaction"]
> [Aggiornare un'applicazione in Kubernetes](./container-service-tutorial-kubernetes-app-update.md)

