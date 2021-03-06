---
title: Come usare Twilio per le funzionalità voce e SMS (PHP) | Microsoft Docs
description: Informazioni su come effettuare una chiamata telefonica e inviare un SMS con il servizio API Twilio API in Azure. Gli esempi di codice sono scritti in PHP.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 34057f1962338927a252011dccc56ed6a77bec47
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636032"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Come usare Twilio per le funzionalità voce ed SMS in PHP
In questa guida viene illustrato come eseguire attività di programmazione comuni con il servizio API Twilio in Azure. Gli scenari presentati includono la composizione di una chiamata telefonica e l'invio di un messaggio SMS (Short Message Service). Per altre informazioni su Twilio e sull'utilizzo delle funzionalità voce ed SMS nelle applicazioni, vedere la sezione [Passaggi successivi](#NextSteps) .

## <a id="WhatIs"></a>Informazioni su Twilio
Twilio è una tecnologia all'avanguardia per le comunicazioni aziendali che consente agli sviluppatori di incorporare funzionalità voce, VoIP e di messaggistica nelle applicazioni. Consente di virtualizzare tutta l'infrastruttura necessaria in un ambiente globale basato su cloud, esponendolo attraverso la piattaforma API per le comunicazioni Twilio. Le applicazioni sono scalabili e facili da compilare. Offre flessibilità, grazie a un modello di prezzi con pagamento al consumo, e l'affidabilità del cloud.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di inviare e ricevere messaggi di testo. **Twilio Client** consente di effettuare chiamate VoIP da qualsiasi telefono, tablet o browser e supporta WebRTC.

## <a id="Pricing"></a>Prezzi e offerte speciali di Twilio
I clienti di Azure riceveranno un' [offerta speciale](https://www.twilio.com/azure): $ 10 di credito Twilio all'aggiornamento dell'account Twilio. Il credito Twilio può essere applicato a qualsiasi utilizzo di Twilio ($ 10 di credito equivalgono all'invio di 1.000 SMS o a 1.000 minuti voce per le chiamate in entrata, a seconda della località del numero di telefono, del messaggio o della destinazione della chiamata). Riscattare il credito Twilio e iniziare a usare il servizio su: [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure).

Twilio è un servizio con pagamento in base al consumo. Non prevede spese iniziali ed è possibile chiudere l'account in qualsiasi momento. Per altre informazioni, vedere la pagina relativa ai [prezzi di Twilio][twilio_pricing].

## <a id="Concepts"></a>Concetti
L'API Twilio è un'API RESTful che fornisce funzionalità voce ed SMS per le applicazioni. Le librerie client sono disponibili in più lingue. per un elenco, vedere [TWILIO API Libraries][twilio_libraries].

I concetti principali dell'API Twilio sono costituiti dai verbi Twilio e dal linguaggio di markup Twilio (Twilio Markup Language, TwiML).

### <a id="Verbs"></a>Verbi Twilio
Nell'API vengono usati i verbi di Twilio: il verbo **&lt;Say&gt;** , ad esempio, indica a Twilio di recapitare un messaggio acustico in una chiamata.

Di seguito è riportato un elenco dei verbi Twilio. Per altre informazioni su altri verbi e funzionalità, vedere la [documentazione relativa a Twilio Markup Language](https://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;** : connette il chiamante a un altro telefono.
* **&lt;Gather&gt;** : raccoglie i numeri digitati sulla tastiera del telefono.
* **&lt;Hangup&gt;** : termina una chiamata.
* **&lt;Play&gt;** : riproduce un file audio.
* **&lt;Pause&gt;** : attende in modo silenzioso per un numero di secondi specificato.
* **&lt;Record&gt;** : registra la voce del chiamante e restituisce l'URL di un file contenente la registrazione.
* **&lt;Redirect&gt;** : trasferisce il controllo di una chiamata o di un SMS al codice TwiML presso un URL diverso.
* **&lt;Reject&gt;** : rifiuta una chiamata in arrivo al numero Twilio senza alcun addebito.
* **&lt;Say&gt;** : effettua la sintesi vocale del testo durante una chiamata.
* **&lt;Sms&gt;** : invia un SMS.

### <a id="TwiML"></a>TwiML
TwiML è un set di istruzioni basate su XML e sui verbi Twilio che indicano a Twilio come elaborare una chiamata o un SMS.

Ad esempio, il codice TwiML seguente effettua la sintesi vocale del testo **Hello World** .

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando l'applicazione chiama l'API Twilio, uno dei parametri dell'API è l'URL che restituisce la risposta TwiML. Ai fini dello sviluppo, è possibile utilizzare gli URL offerti da Twilio per fornire le risposte TwiML utilizzate dalle applicazioni. È inoltre possibile ospitare gli URL per produrre le risposte TwiML oppure utilizzare l'oggetto **TwiMLResponse** .

Per ulteriori informazioni sui verbi Twilio, i relativi attributi e TwiML, vedere [TwiML][twiml]. Per altre informazioni sull'API Twilio, vedere [API Twilio][twilio_api].

## <a id="CreateAccount"></a>Creare un account Twilio
Quando si è pronti per ottenere un account Twilio, iscriversi a [provare Twilio][try_twilio]. È possibile iniziare con un account gratuito ed eseguire l'aggiornamento in un secondo momento.

Quando si effettua l'iscrizione a un account Twilio, si riceverà un ID account e un token di autenticazione. Entrambe queste informazioni sono necessarie per effettuare chiamate all'API Twilio. Per prevenire accessi autorizzati all'account, conservare il token di autenticazione in un luogo sicuro. L'ID account e il token di autenticazione sono visualizzabili nella [pagina dell'account Twilio][twilio_account], rispettivamente nei campi **account SID** e **auth token**.

## <a id="create_app"></a>Creare un'applicazione PHP
Un'applicazione PHP che usa il servizio Twilio e viene eseguita in Azure non è diversa da qualsiasi altra applicazione PHP che usa il servizio Twilio. Sebbene i servizi Twilio siano basati su REST e possano essere chiamati da PHP in diversi modi, in questo articolo verrà illustrato come usare i servizi Twilio con la [libreria Twilio per php da GitHub][twilio_php]. Per ulteriori informazioni sull'utilizzo della libreria Twilio per PHP, vedere [https://www.twilio.com/docs/libraries/php][twilio_lib_docs].

Istruzioni dettagliate per la compilazione e la distribuzione di un'applicazione Twilio/PHP in Azure sono disponibili in [come effettuare una telefonata usando Twilio in un'applicazione php in Azure][howto_phonecall_php].

## <a id="configure_app"></a>Configurare l'applicazione per l'uso delle librerie Twilio
È possibile configurare l'applicazione in modo che usi la libreria Twilio per PHP in due modi:

1. Scaricare la libreria Twilio per php da GitHub ([https://github.com/twilio/twilio-php][twilio_php]) e aggiungere la directory **Services** all'applicazione.
   
    -OPPURE-
2. Installare la libreria Twilio per PHP come pacchetto PEAR. È possibile eseguire l'installazione tramite i comandi seguenti:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Dopo avere installato la libreria Twilio per PHP, sarà possibile aggiungere un'istruzione **require_once** nella parte superiore del file PHP, in modo che faccia riferimento alla libreria:

        require_once 'Services/Twilio.php';

Per altre informazioni, vedere [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Procedura: Effettuare una chiamata in uscita
Di seguito è illustrato come effettuare una chiamata in uscita tramite la classe **Services_Twilio**. Questo codice utilizza inoltre un sito fornito da Twilio per restituire la risposta TwiML (Twilio Markup Language). Sostituire i valori per i numeri di telefono **From** e **To** e assicurarsi di verificare il numero di telefono in **From** per l'account Twilio prima di eseguire il codice.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the call.
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "https://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Come indicato in precedenza, questo codice utilizza un sito fornito da Twilio per restituire la risposta TwiML. Per fornire la risposta TwiML è inoltre possibile usare il proprio sito. Per altre informazioni, vedere [Come fornire risposte TwiML dal proprio sito Web](#howto_provide_twiml_responses).

* **Nota**: Per risolvere i problemi relativi agli errori di convalida del certificato SSL, vedere[http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a id="howto_send_sms"></a>Procedura: Inviare un messaggio SMS
Nella schermata seguente è illustrato come inviare un SMS tramite la classe **Services_Twilio**. Il numero in **From** per l'invio di messaggi SMS con gli account di valutazione è fornito da Twilio. Il numero in **To** deve essere verificato per l'account Twilio prima di eseguire il codice.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a id="howto_provide_twiml_responses"></a>Procedura: Fornire risposte TwiML dal proprio sito Web
Quando l'applicazione avvia una chiamata all'API Twilio, Twilio invia la richiesta a un URL che deve restituire una risposta TwiML. Nell'esempio precedente viene usato l'URL [https://twimlets.com/message][twimlet_message_url]fornito da Twilio. Poiché TwiML è progettato per essere usato da Twilio, è possibile visualizzarlo nel browser. Ad esempio, fare [https://twimlets.com/message][twimlet_message_url] clic per visualizzare un `<Response>` elemento vuoto; come altro esempio, [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] fare clic per `<Response>` visualizzare un elemento che `<Say>` contiene un elemento.

Anziché utilizzare l'URL fornito da Twilio, è possibile creare un sito personalizzato che restituisce risposte HTTP. È possibile creare il sito in qualsiasi linguaggio che restituisca risposte XML. In questo argomento si presuppone che si userà PHP per creare TwiML.

La pagina PHP seguente crea una risposta TwiML che pronuncia **Hello World** nella chiamata.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Come si evince dal codice riportato sopra, la risposta TwiML è semplicemente un documento XML. La libreria Twilio per PHP contiene le classi che consentono di generare automaticamente le istruzioni TwiML. Nell'esempio seguente viene creata la stessa risposta descritta sopra, ma usando la classe **Services\_Twilio\_Twiml** nella libreria Twilio per PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Per altre informazioni su TwiML, vedere [https://www.twilio.com/docs/api/twiml][twiml_reference]. 

Dopo aver configurato la pagina PHP in modo da fornire risposte TwiML, usare l'URL della pagina PHP come URL trasmesso nel metodo `Services_Twilio->account->calls->create`. Se, ad esempio, si dispone di un'applicazione Web chiamata **MyTwiML** distribuita in un servizio ospitato in Azure e il nome della pagina PHP è **mytwiml.php**, è possibile trasmettere l'URL a **Services_Twilio->account->calls->create**, come mostrato nell'esempio seguente:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Per altre informazioni sull'uso di Twilio in Azure con PHP, vedere [come effettuare una telefonata usando Twilio in un'applicazione php in Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Procedura: Usare servizi Twilio aggiuntivi
Oltre agli esempi illustrati in questa pagina, Twilio offre API basate su Web che è possibile utilizzare per sfruttare altre funzionalità di Twilio dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione dell'API Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Passaggi successivi
Dopo aver appreso le nozioni di base sul servizio Twilio, utilizzare i collegamenti seguenti per ulteriori informazioni:

* [Linee guida sulla sicurezza di Twilio][twilio_security_guidelines]
* [Twilio HowTo e codice di esempio][twilio_howtos]
* [Esercitazioni introduttive su Twilio][twilio_quickstarts] 
* [Twilio su GitHub][twilio_on_github]
* [Comunicare con il supporto di Twilio][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
