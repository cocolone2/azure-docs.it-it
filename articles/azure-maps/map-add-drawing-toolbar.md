---
title: Aggiungere una barra degli strumenti di disegno a una mappa | Mappe Microsoft Azure
description: Come aggiungere una barra degli strumenti di disegno a una mappa usando Azure Maps Web SDK
author: farah-alyasari
ms.author: v-faalya
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cb0f70bc42c9ac0f7026c910593950516f027a88
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209750"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Aggiungere una barra degli strumenti di disegno a una mappa

Questo articolo illustra come usare il modulo strumenti di disegno e visualizzare la barra degli strumenti di disegno sulla mappa. Il controllo [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) aggiunge la barra degli strumenti di disegno sulla mappa. Si apprenderà come creare mappe con solo uno e tutti gli strumenti di disegno e come personalizzare il rendering delle forme di disegno nel gestore del disegno.

## <a name="add-drawing-toolbar"></a>Aggiungere la barra degli strumenti di disegno

Il codice seguente consente di creare un'istanza del gestore di disegno e di visualizzare la barra degli strumenti sulla mappa.

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Di seguito è riportato l'esempio di codice completo dell'esecuzione della funzionalità precedente:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Aggiungere la barra degli strumenti di disegno" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>barra degli strumenti Aggiungi disegno</a> di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Limita le opzioni visualizzate della barra degli strumenti

Il codice seguente consente di creare un'istanza del gestore di disegno e di visualizzare la barra degli strumenti solo con uno strumento di disegno poligono sulla mappa. 

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Di seguito è riportato l'esempio di codice completo dell'esecuzione della funzionalità precedente:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Aggiungere uno strumento di disegno poligono" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>aggiungere uno strumento di disegno poligono</a> da mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Modificare lo stile di rendering del disegno

Il codice seguente ottiene i livelli di rendering dal gestore del disegno e modifica le opzioni per modificare lo stile di rendering per il disegno. In questo caso, verrà eseguito il rendering dei punti con un'icona del marcatore blu. Le linee saranno di colore rosso e di quattro pixel. I poligoni avranno un colore di riempimento verde e un contorno arancione.

```Javascript
var layers = drawingManager.getLayers();
    layers.pointLayer.setOptions({
        iconOptions: {
            image: 'marker-blue'
        }
    });
    layers.lineLayer.setOptions({
        strokeColor: 'red',
        strokeWidth: 4
    });
    layers.polygonLayer.setOptions({
        fillColor: 'green'
    });
    layers.polygonOutlineLayer.setOptions({
        strokeColor: 'orange'
    });
```

Di seguito è riportato l'esempio di codice completo dell'esecuzione della funzionalità precedente:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Modificare lo stile di rendering del disegno" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere lo <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>stile di rendering del disegno di modifica</a> della penna di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare le funzionalità aggiuntive del modulo strumenti di disegno:

> [!div class="nextstepaction"]
> [Ottenere i dati di forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagire agli eventi di disegno](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipi di interazione e tasti di scelta rapida](drawing-tools-interactions-keyboard-shortcuts.md)

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Barra degli strumenti disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Gestione disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)
