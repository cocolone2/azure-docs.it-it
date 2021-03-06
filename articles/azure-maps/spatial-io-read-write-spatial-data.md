---
title: Lettura e scrittura dei dati spaziali | Mappe Microsoft Azure
description: Informazioni su come leggere e scrivere dati usando il modulo di i/o spaziale, fornito da Azure Maps Web SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 458b307cf1158c467100e032e3f789462e8fdcca
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370908"
---
# <a name="read-and-write-spatial-data"></a>Lettura e scrittura dei dati spaziali

Nella tabella seguente sono elencati i formati di file spaziali supportati per la lettura e la scrittura di operazioni con il modulo IO spaziale.

| Formato dati       | Lettura | Scrittura |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| CSV spaziale       | ✓  |  ✓  |
| Testo noto   | ✓  |  ✓  |

Queste sezioni successive descrivono tutti i diversi strumenti per la lettura e la scrittura dei dati spaziali usando il modulo di i/o spaziale.

## <a name="read-spatial-data"></a>Lettura dei dati spaziali

La funzione `atlas.io.read` è la funzione principale usata per leggere i formati di dati spaziali comuni, ad esempio file KML, GPX, GeoRSS, GeoJSON e CSV con dati spaziali. Questa funzione può anche leggere le versioni compresse di questi formati come file zip o file KMZ. Il formato del file KMZ è una versione compressa di KML che può includere anche asset come le immagini. In alternativa, la funzione Read può assumere un URL che punta a un file in uno di questi formati. Gli URL devono essere ospitati in un endpoint abilitato per CORs o fornire un servizio proxy nelle opzioni di lettura. Il servizio proxy viene usato per caricare le risorse nei domini che non sono abilitati per CORs. La funzione Read restituisce un suggerimento per aggiungere le icone dell'immagine alla mappa ed elabora i dati in modo asincrono per ridurre al minimo l'effetto sul thread dell'interfaccia utente.

Quando si legge un file compresso, come zip o KMZ, questo verrà decompresso e analizzato per il primo file valido. Ad esempio, doc. KML o un file con altre estensioni valide, ad esempio:. KML,. XML, GeoJSON,. JSON,. csv,. TSV o. txt. Quindi, le immagini a cui viene fatto riferimento nei file KML e GeoRSS vengono precaricate per assicurarsi che siano accessibili. I dati di immagine inaccessibili possono caricare un'immagine di fallback alternativa o verranno rimossi dagli stili. Le immagini estratte dai file KMZ verranno convertite in URI di dati.

Il risultato della funzione Read è un oggetto `SpatialDataSet`. Questo oggetto estende la classe Featurecollection di GeoJSON. È possibile passare facilmente a un `DataSource` così come sono per eseguire il rendering delle relative funzionalità su una mappa. Il `SpatialDataSet` non solo contiene informazioni sulle funzionalità, ma può includere anche sovrapposizioni di base KML, metriche di elaborazione e altri dettagli, come illustrato nella tabella seguente.

| Nome proprietà | Type | Descrizione | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Rettangolo di delimitazione di tutti i dati nel set di dati. |
| `features` | `Feature[]` | Funzionalità GeoJSON all'interno del set di dati. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Una matrice di GroundOverlays KML. |
| `icons` | Record&lt;stringa, stringa&gt; | Set di URL di icone. Chiave = nome icona, valore = URL. |
| connessione | any | Informazioni sulle proprietà fornite a livello di documento di un set di dati spaziali. |
| `stats` | `SpatialDataSetStats` | Statistiche relative al contenuto e al tempo di elaborazione di un set di dati spaziali. |
| `type` | `'FeatureCollection'` | Valore di tipo GeoJSON di sola lettura. |

## <a name="examples-of-reading-spatial-data"></a>Esempi di lettura dei dati spaziali

Nel codice seguente viene illustrato come leggere un set di dati spaziali semplice ed eseguirne il rendering sulla mappa utilizzando la classe `SimpleDataLayer`. Il codice usa un file GPX a cui punta un URL.

<br/>

<iframe height='500' scrolling='no' title='Caricamento semplice dei dati spaziali' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere la pagina relativa ai <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>dati spaziali</a> per il caricamento delle penne semplici da Maps di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

La demo del codice seguente mostra come leggere e caricare il codice KML, o KMZ, nella mappa. Il linguaggio KML può contenere sovrapposizioni di base, che saranno sotto forma di `ImageLyaer` o `OgcMapLayer`. È necessario aggiungere tali sovrimpressioni sulla mappa separatamente dalle funzionalità. Inoltre, se il set di dati include icone personalizzate, queste icone devono essere caricate nelle risorse Maps prima del caricamento delle funzionalità.

<br/>

<iframe height='500' scrolling='no' title='Carica il formato KML nella mappa' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere la pagina relativa al caricamento di un <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>KML nella mappa</a> tramite mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Facoltativamente, è possibile fornire un servizio proxy per l'accesso a risorse tra domini che potrebbero non essere abilitate per CORs. Questo frammento di codice mostra che è possibile fornire un servizio proxy:

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

//Read a KML file from a URL or pass in a raw KML string.
atlas.io.read('https://s3-us-west-2.amazonaws.com/s.cdpn.io/1717245/2007SanDiegoCountyFires.kml', {
    //Provide a proxy service
    proxyService: proxyServiceUrl
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

L'ultima demo seguente mostra come leggere un file delimitato ed eseguirne il rendering nella mappa. In questo caso, il codice utilizza un file CSV con colonne di dati spaziali.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un file delimitato' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere la penna <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>aggiungere un file delimitato</a> da mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Scrivere dati spaziali

Nel modulo di i/o spaziale sono presenti due funzioni di scrittura principali. La funzione `atlas.io.write` genera una stringa, mentre la funzione `atlas.io.writeCompressed` genera un file zip compresso. Il file zip compresso conterrebbe un file basato su testo con i dati spaziali. Entrambe queste funzioni restituiscono un suggerimento per aggiungere i dati al file. Entrambi possono scrivere i dati seguenti: `SpatialDataSet`, `DataSource`, `ImageLayer`, `OgcMapLayer`, raccolta di funzionalità, funzionalità, geometria o una matrice di qualsiasi combinazione di questi tipi di dati. Quando si scrive usando entrambe le funzioni, è possibile specificare il formato di file desiderato. Se il formato del file non è specificato, i dati verranno scritti come KML.

Lo strumento seguente illustra la maggior parte delle opzioni di scrittura che è possibile usare con la funzione `atlas.io.write`.

<br/>

<iframe height='700' scrolling='no' title='Opzioni di scrittura dei dati spaziali' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere le <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>Opzioni di scrittura dei dati spaziali</a> di Pen da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Esempio di scrittura di dati spaziali

L'esempio seguente consente di trascinare e rilasciare i file spaziali sulla mappa. È possibile esportare i dati GeoJSON dalla mappa e scriverli in uno dei formati di dati spaziali supportati come stringa o come file compresso.

<br/>

<iframe height='700' scrolling='no' title='Trascinare e rilasciare i file spaziali nella mappa' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere la pagina relativa al <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>trascinamento della selezione dei file spaziali su Map</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Facoltativamente, è possibile fornire un servizio proxy per l'accesso a risorse tra domini che potrebbero non essere abilitate per CORs. Questo frammento di codice mostra che è possibile incorporare un servizio proxy:

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

function readData(data, fileName) {
    loadingIcon.style.display = '';
    fileCount++;
    //Attempt to parse the file and add the shapes to the map.
    atlas.io.read(data, {
        //Provide a proxy service
        proxyService: proxyServiceUrl
    }).then(
        //Success
        function(r) {
            //some code goes here ...
        }
    );
}
```

## <a name="read-and-write-well-known-text-wkt"></a>Lettura e scrittura di testo ben noto (WKT)

[Well known text](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) è uno standard Open Geospatial Consortium (OGC) per la rappresentazione di geometrie spaziali come testo. Molti sistemi geospaziali supportano WKT, ad esempio Azure SQL e Azure PostgreSQL, usando il plug-in PostGIS. Come la maggior parte degli standard OGC, le coordinate vengono formattate come "Latitudine Longitudine" per essere allineate con la convenzione "x y". Ad esempio, un punto alla Longitudine 110 e la latitudine 45 possono essere scritti come `POINT(-110 45)` usando il formato WKT.

Il testo noto può essere letto usando la funzione `atlas.io.ogc.WKT.read` e scritto usando la funzione `atlas.io.ogc.WKT.write`.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Esempi di lettura e scrittura di testo ben noto (WKT)

Nel codice seguente viene illustrato come leggere la stringa di testo nota `POINT(-122.34009 47.60995)` e come eseguirne il rendering sulla mappa utilizzando un livello Bubble.

<br/>

<iframe height='500' scrolling='no' title='Leggi testo ben noto' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>testo noto</a> per la lettura di Pen da mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice seguente viene illustrata la lettura e la scrittura di testo ben noto.

<br/>

<iframe height='700' scrolling='no' title='Lettura e scrittura di testo ben noto' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere la pagina relativa alla <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>lettura e scrittura di testo note</a> da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>Leggi e Scrivi GML

GML è una specifica del file XML spaziale spesso utilizzata come estensione di altre specifiche XML. I dati GeoJSON possono essere scritti come XML con i tag GML usando la funzione `atlas.io.core.GmlWriter.write`. Il codice XML che contiene GML può essere letto usando la funzione `atlas.io.core.GmlReader.read`. La funzione Read ha due opzioni:

- L'opzione `isAxisOrderLonLat`: l'ordine dell'asse delle coordinate "latitudine, Longitudine" o "Longitudine, latitudine" può variare tra i set di dati e non è sempre ben definito. Per impostazione predefinita, il lettore GML legge i dati delle coordinate come "latitudine, Longitudine", ma l'impostazione di questa opzione su true lo leggerà come "Longitudine, latitudine".
- Opzione `propertyTypes`: questa opzione è una tabella di ricerca del valore chiave in cui la chiave è il nome di una proprietà nel set di dati. Il valore è il tipo di oggetto su cui eseguire il cast del valore durante l'analisi. I valori di tipo supportati sono: `string`, `number`, `boolean`e `date`. Se una proprietà non è presente nella tabella di ricerca o se il tipo non è definito, la proprietà verrà analizzata come stringa.

Per impostazione predefinita, la funzione `atlas.io.read` viene impostata sulla funzione `atlas.io.core.GmlReader.read` quando rileva che i dati di input sono XML, ma i dati non sono uno degli altri formati XML spaziali di supporto.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [funzioni statiche atlas.io](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [funzioni Atlas. io. OGC. WKT](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un livello mappa OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Connettersi a un servizio WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Sfruttare le operazioni principali](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Dettagli del formato dati supportati](spatial-io-supported-data-format-details.md)
