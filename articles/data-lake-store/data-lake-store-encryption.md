---
title: Crittografia in Azure Data Lake Storage Gen1 | Microsoft Docs
description: La crittografia in Azure Data Lake Storage Gen1 consente di proteggere i dati, implementare criteri di sicurezza aziendali e soddisfare i requisiti di conformità normativi. Questo articolo offre una panoramica della progettazione e illustra alcuni aspetti tecnici dell'implementazione.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: a009f212bd8baaa353d602dc6090aeeccddd4936
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878452"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Crittografia dei dati in Azure Data Lake Storage Gen1

La crittografia in Azure Data Lake Storage Gen1 consente di proteggere i dati, implementare criteri di sicurezza aziendali e soddisfare i requisiti di conformità normativi. Questo articolo offre una panoramica della progettazione e illustra alcuni aspetti tecnici dell'implementazione.

Data Lake Storage Gen1 supporta la crittografia dei dati sia inattivi che in transito. Per i dati inattivi, Data Lake Storage Gen1 supporta la crittografia trasparente "attivata per impostazione predefinita". Di seguito è riportata una spiegazione più dettagliata:

* **In per impostazione predefinita**: Quando si crea un nuovo account Data Lake archiviazione Gen1, l'impostazione predefinita consente la crittografia. I dati archiviati in Data Lake Storage Gen1 quindi vengono sempre crittografati prima essere archiviati in supporti persistenti. Questo comportamento è valido per tutti i dati e non può essere modificato dopo che un account è stato creato.
* **Trasparente**: Data Lake archiviazione Gen1 automaticamente crittografa i dati prima di renderli persistenti e li decrittografa prima del recupero. La crittografia viene configurata e gestita a livello di account Data Lake Storage Gen1 da un amministratore. Non vengono apportate modifiche alle API di accesso ai dati. Non sono quindi necessarie modifiche nelle applicazioni e nei servizi che interagiscono con Data Lake Storage Gen1 a causa della crittografia.

Anche i dati in transito (noti anche come dati in movimento) vengono sempre crittografati in Data Lake Storage Gen1. I dati, oltre a essere crittografati prima dell'archiviazione in supporti persistenti, vengono sempre protetti anche mentre sono in transito usando HTTPS. HTTPS è l'unico protocollo supportato per le interfacce REST di Data Lake Storage Gen1. Il diagramma seguente illustra come vengono crittografati i dati in Data Lake Storage Gen1:

![Diagramma della crittografia dei dati in Data Lake Storage Gen1](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Configurare la crittografia con Data Lake Storage Gen1

La crittografia per Data Lake Storage Gen1 viene configurata durante la creazione di un account ed è sempre abilitata per impostazione predefinita. È possibile gestire le chiavi manualmente o consentire a Data Lake Storage Gen1 di gestirle automaticamente (impostazione predefinita).

Per altre informazioni, vedere la [Introduzione](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Come funziona la crittografia in Data Lake Storage Gen1

Le informazioni seguenti illustrano come gestire le chiavi di crittografia master e i tre diversi tipi di chiavi che è possibile usare nella crittografia dei dati per Data Lake Storage Gen1.

### <a name="master-encryption-keys"></a>Chiavi di crittografia master

Data Lake Storage Gen1 offre due modalità per la gestione delle chiavi di crittografia master (MEK). Per il momento si supponga che la chiave di crittografia sia la chiave principale. L'accesso alla chiave di crittografia master è necessario per decrittografare i dati archiviati in Data Lake Storage Gen1.

Le due modalità per la gestione della chiavi di crittografia master sono le seguenti:

*   Chiavi gestite dal servizio
*   Chiavi gestite dal cliente

In entrambe le modalità la chiave di crittografia master viene protetta archiviandola in Azure Key Vault. Key Vault è un servizio di Azure completamente gestito a sicurezza elevata che può essere usato per proteggere le chiavi crittografiche. Per altre informazioni, vedere [Key Vault](https://azure.microsoft.com/services/key-vault).

Ecco un breve confronto delle funzionalità offerte dalle due modalità di gestione delle chiavi di crittografia master.

|  | Chiavi gestite dal servizio | Chiavi gestite dal cliente |
| --- | --- | --- |
|Come vengono archiviati i dati?|Vengono sempre crittografati prima dell'archiviazione.|Vengono sempre crittografati prima dell'archiviazione.|
|Dove viene archiviata la chiave di crittografia master?|Key Vault|Key Vault|
|Vengono archiviate chiavi di crittografia in chiaro al di fuori di Key Vault? |No|No|
|La chiave di crittografia master può essere recuperata da Key Vault?|No. La chiave di crittografia master, dopo essere stata archiviata in Key Vault, può solo essere usata per la crittografia e la decrittografia.|No. La chiave di crittografia master, dopo essere stata archiviata in Key Vault, può solo essere usata per la crittografia e la decrittografia.|
|Chi è il proprietario dell'istanza di Key Vault e della chiave di crittografia master?|Il servizio Data Lake Storage Gen1|L'utente è il proprietario dell'istanza di Key Vault, che appartiene alla sottoscrizione di Azure. La chiave di crittografia master in Key Vault può essere gestita dal software o dall'hardware.|
|È possibile revocare l'accesso alla chiave di crittografia master (MEK) per il servizio Data Lake Storage Gen1?|No|Sì. È possibile gestire gli elenchi di controllo di accesso in Key Vault e rimuovere le voci di controllo di accesso nell'identità del servizio per il servizio Data Lake Storage Gen1.|
|È possibile eliminare definitivamente la chiave di crittografia master?|No|Sì. Se si elimina la chiave di crittografia master da Key Vault, i dati nell'account Data Lake Storage Gen1 non possono essere in alcun modo decrittografati, neppure dal servizio Data Lake Storage Gen1. <br><br> Se è stato eseguito un backup esplicito della chiave di crittografia master prima di eliminarla da Key Vault, la chiave può essere ripristinata e i dati recuperati. Se tuttavia non è stato eseguito un backup della chiave di crittografia master prima di eliminarla da Key Vault, i dati nell'account Data Lake Storage Gen1 non potranno più essere decrittografati.|


Fatta eccezione per questa differenza, ovvero chi gestisce la chiave di crittografia master e l'istanza di Key Vault in cui si trova, la progettazione è la medesima per entrambe le modalità.

È importante tenere presente quanto segue quando si sceglie la modalità per le chiavi di crittografia master:

*   È possibile scegliere se usare chiavi gestite dal cliente o dal servizio quando si effettua il provisioning di un account Data Lake Storage Gen1.
*   Dopo il provisioning di un account Data Lake Storage Gen1, non è possibile modificare la modalità.

### <a name="encryption-and-decryption-of-data"></a>Crittografia e decrittografia dei dati

Nella progettazione della crittografia dei dati vengono usati tre tipi di chiavi. La tabella seguente contiene un riepilogo:

| Chiave                   | Abbreviazione | Elemento associato | Posizione di archiviazione                             | Type       | Note                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Chiave di crittografia master | MEK          | Un account Data Lake Storage Gen1 | Key Vault                              | Asimmetrica | Può essere gestita da Data Lake Storage Gen1 o dall'utente.                                                              |
| Chiave di crittografia dei dati   | DEK          | Un account Data Lake Storage Gen1 | Risorsa di archiviazione persistente, gestita dal servizio Data Lake Storage Gen1 | Simmetrica  | La chiave di crittografia dei dati viene crittografata dalla chiave di crittografia master. È la chiave di crittografia dei dati crittografata a essere archiviata nei supporti persistenti. |
| Chiave di crittografia a blocchi  | BEK          | Un blocco di dati | Nessuna                                         | Simmetrica  | La chiave di crittografia a blocchi deriva dalla chiave di crittografia dei dati e dal blocco di dati.                                                      |

Il diagramma seguente illustra questi concetti:

![Chiavi nella crittografia dei dati](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudoalgoritmo quando un file deve essere decrittografato:
1.  Controllare se la chiave di crittografia dei dati per l'account Data Lake Storage Gen1 è memorizzata nella cache e può essere usata.
    - In caso contrario, leggere la chiave di crittografia dei dati crittografata dalla risorsa di archiviazione persistente e inviarla a Key Vault per la decrittografia. Memorizzare nella cache la chiave di crittografia dei dati decrittografata. Ora è possibile usarla.
2.  Per ogni blocco di dati nel file:
    - Leggere il blocco di dati crittografato da una risorsa di archiviazione persistente.
    - Generare la chiave di crittografia a blocchi dalla chiave di crittografia dei dati e dal blocco di dati crittografato.
    - Usare la chiave di crittografia a blocchi per decrittografare i dati.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudoalgoritmo quando un file deve essere crittografato:
1.  Controllare se la chiave di crittografia dei dati per l'account Data Lake Storage Gen1 è memorizzata nella cache e può essere usata.
    - In caso contrario, leggere la chiave di crittografia dei dati crittografata dalla risorsa di archiviazione persistente e inviarla a Key Vault per la decrittografia. Memorizzare nella cache la chiave di crittografia dei dati decrittografata. Ora è possibile usarla.
2.  Generare una chiave di crittografia a blocchi univoca per il blocco di dati dalla chiave di crittografia dei dati.
3.  Crittografare il blocco di dati con la chiave di crittografia a blocchi usando la crittografia AES-256.
4.  Archiviare il blocco di dati crittografato in una risorsa di archiviazione persistente.

> [!NOTE] 
> La chiave di crittografia dei dati viene sempre archiviata crittografata dalla chiave di crittografia master nei supporti persistenti o memorizzata nella cache in memoria.

## <a name="key-rotation"></a>Rotazione delle chiavi

Quando si usano le chiavi gestite dal cliente, è possibile ruotare la chiave di crittografia master. Per informazioni su come configurare un account Data Lake Storage Gen1 con chiavi gestite dal cliente, vedere [Introduzione](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Prerequisiti

Quando si è configurato l'account Data Lake Storage Gen1, si è scelto di usare le proprie chiavi. Questa opzione non può essere modificata dopo la creazione dell'account. I passaggi seguenti presuppongono che si usino chiavi gestite dal cliente, ovvero che si siano scelte le chiavi da Key Vault.

Tenere presente che, se si usano le opzioni predefinite per la crittografia, i dati vengono sempre crittografati usando le chiavi gestite da Data Lake Storage Gen1. Con questa opzione l'utente non può ruotare le chiavi perché sono gestite da Data Lake Storage Gen1.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>Come ruotare la chiave di crittografia master (MEK) in Data Lake Storage Gen1

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare all'istanza di Key Vault in cui sono archiviate le chiavi associate all'account Data Lake Storage Gen1. Selezionare **Chiavi**.

    ![Screenshot di Key Vault](./media/data-lake-store-encryption/keyvault.png)

3. Selezionare la chiave associata all'account Data Lake Storage Gen1 e creare una nuova versione di questa chiave. Si noti che Data Lake Storage Gen1 attualmente supporta solo la rotazione a una nuova versione di una chiave. Non supporta la rotazione a una chiave diversa.

   ![Screenshot della finestra Chiavi con Nuova versione in evidenza](./media/data-lake-store-encryption/keynewversion.png)

4. Passare all'account Data Lake Storage Gen1 e selezionare **Crittografia**.

   ![Screenshot della finestra dell'account Data Lake Storage Gen1 con Crittografia in evidenza](./media/data-lake-store-encryption/select-encryption.png)

5. Un messaggio notifica che è disponibile una nuova versione della chiave. Fare clic su **Ruota chiave** per aggiornare la chiave alla nuova versione.

   ![Screenshot della finestra Data Lake Storage Gen1 con il messaggio e Ruota chiave in evidenza](./media/data-lake-store-encryption/rotatekey.png)

Questa operazione richiederà meno di due minuti e non sono previsti tempi di inattività a causa della rotazione della chiave. Al termine dell'operazione, sarà in uso la nuova versione della chiave.

> [!IMPORTANT]
> Al termine dell'operazione di rotazione delle chiavi, la versione precedente della chiave non viene più usata attivamente per la crittografia dei dati.  In rari casi di errori imprevisti che interessano anche le copie ridondanti dei dati, è tuttavia possibile che i dati vengano ripristinati da un backup che usa ancora la chiave precedente. Per assicurare che i dati siano accessibili in tali circostanze rare, mantenere una copia della versione precedente della chiave di crittografia. Per procedure consigliate per la pianificazione del ripristino di emergenza, vedere [Linee guida per il ripristino di emergenza in Data Lake Storage Gen1](data-lake-store-disaster-recovery-guidance.md). 