---
title: "Avvio rapido: Uso di PHP per chiamare l'API Analisi del testo"
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido illustra come ottenere informazioni ed esempi di codice per iniziare rapidamente a usare l'API Analisi del testo in Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: a74cdb79da668cdba44c051c7b7eada3ace8abe4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75378586"
---
# <a name="quickstart-using-php-to-call-the-text-analytics-cognitive-service"></a>Avvio rapido: Uso di PHP per chiamare il servizio cognitivo Analisi del testo
<a name="HOLTop"></a>

Questo articolo illustra come [rilevare la lingua](#Detect), [analizzare il sentiment](#SentimentAnalysis), [estrarre frasi chiave](#KeyPhraseExtraction) e [identificare le entità collegate](#Entities) usando le  [API Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759711)  con PHP.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="Detect"></a>

## <a name="detect-language"></a>Rileva lingua

L'API Rilevamento lingua rileva la lingua di un documento di testo usando il [metodo per il rilevamento della lingua](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

1. Creare un nuovo progetto PHP nell'ambiente di sviluppo integrato preferito.
1. Aggiungere il codice riportato di seguito.
1. Copiare la chiave e l'endpoint di Analisi del testo nel codice.
1. Eseguire il programma.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"

$subscription_key = "<paste-your-text-analytics-key-here>";
$endpoint = "<paste-your-text-analytics-endpoint-here>";

$path = '/text/analytics/v2.1/languages';

function DetectLanguage ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'text' => 'This is a document written in English.' ),
        array ( 'id' => '2', 'text' => 'Este es un document escrito en Español.' ),
        array ( 'id' => '3', 'text' => '这是一个用中文写的文件')
    )
);

print "Please wait a moment for the results to appear.";

$result = DetectLanguage ($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Risposta di Rilevamento lingua**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}


```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analizzare la valutazione

L'API Analisi del sentiment rileva il sentiment di un set di record di testo usando il [metodo Sentiment](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). L'esempio seguente assegna un punteggio a due documenti, uno in inglese e un altro in spagnolo.


1. Creare un nuovo progetto PHP nell'ambiente di sviluppo integrato preferito.
1. Aggiungere il codice riportato di seguito.
1. Copiare la chiave e l'endpoint di Analisi del testo nel codice.
1. Eseguire il programma.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"
$subscription_key = "<paste-your-text-analytics-key-here>";
$endpoint = "<paste-your-text-analytics-endpoint-here>";

$path = '/text/analytics/v2.1/sentiment';

function GetSentiment ($host, $path, $key, $data) {
    // Make sure all text is UTF-8 encoded.
    foreach ($data as &$item) {
        foreach ($item as $ignore => &$value) {
            $value['text'] = utf8_encode($value['text']);
        }
    }

    $data = json_encode ($data);

    $headers = "Content-type: text/json\r\n" .
        "Content-Length: " . strlen($data) . "\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' ),
        array ( 'id' => '2', 'language' => 'es', 'text' => 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' )
    )
);

print "Please wait a moment for the results to appear.";

$result = GetSentiment($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Risposta di Analisi del sentiment**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Estrarre le frasi chiave

L'API Estrazione frasi chiave consente di estrarre le frasi chiave da un documento di testo usando il [metodo per le frasi chiave](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). L'esempio seguente estrae le frasi chiave per i documenti in inglese e in spagnolo.
1. Creare un nuovo progetto PHP nell'ambiente di sviluppo integrato preferito.
1. Aggiungere il codice riportato di seguito.
1. Copiare la chiave e l'endpoint di Analisi del testo nel codice.
1. Eseguire il programma.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"
$subscription_key = "<paste-your-text-analytics-key-here>";
$endpoint = "<paste-your-text-analytics-endpoint-here>";

$path = '/text/analytics/v2.1/keyPhrases';

function GetKeyPhrases ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' ),
        array ( 'id' => '2', 'language' => 'es', 'text' => 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' ),
        array ( 'id' => '3', 'language' => 'en', 'text' => 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' )
    )
);

print "Please wait a moment for the results to appear.";

$result = GetKeyPhrases($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Risposta di Estrazione frasi chiave**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Identificare le entità 

L'API Entità identifica le entità note in un documento di testo usando il metodo [Entities](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634). L'esempio seguente identifica le entità per i documenti in inglese.

1. Creare un nuovo progetto PHP nell'ambiente di sviluppo integrato preferito.
1. Aggiungere il codice riportato di seguito.
1. Copiare la chiave e l'endpoint di Analisi del testo nel codice. 
1. Eseguire il programma.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"
$subscription_key = "<paste-your-text-analytics-key-here>";
$endpoint = "<paste-your-text-analytics-endpoint-here>";

$path = '/text/analytics/v2.1/entities';

function GetEntities ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Content-Length: " . Length($data) . "\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";
    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'Microsoft is and It company.' ),
    )
);

print "Please wait a moment for the results to appear.";

$result = GetEntities($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Risposta di estrazione di entità**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analisi del testo con Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Vedere anche 

 [Panoramica di Analisi del testo](../overview.md)  
 [Domande frequenti](../text-analytics-resource-faq.md)
