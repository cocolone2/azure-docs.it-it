---
title: Aggiungere una VM Windows Server a un dominio gestito | Microsoft Docs
description: In questa esercitazione si apprenderà come aggiungere una macchina virtuale Windows Server a un dominio gestito di Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2020
ms.author: iainfou
ms.openlocfilehash: 05705d14db336b15a6ddf2317f9e69464c8e575b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378541"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Esercitazione: aggiungere una macchina virtuale Windows Server a un dominio gestito

Azure Active Directory Domain Services (Azure AD DS) offre servizi di dominio gestiti, come l'aggiunta a un dominio, Criteri di gruppo, LDAP e l'autenticazione Kerberos/NTLM, completamente compatibili con Windows Server Active Directory. Con un dominio gestito di Azure Active Directory Domain Services, è possibile rendere disponibili alle macchine virtuali di Azure funzionalità per l'aggiunta a un dominio e la gestione. Questa esercitazione illustra come creare una macchina virtuale Windows Server e quindi aggiungerla a un dominio gestito di Azure Active Directory Domain Services.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una macchina virtuale Windows Server
> * Connettere la macchina virtuale Windows Server a una rete virtuale di Azure
> * Aggiungere la macchina virtuale al dominio gestito di Azure Active Directory Domain Services

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per completare l'esercitazione, sono necessarie le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Un account utente che fa parte del dominio gestito Azure AD DS.
    * Assicurarsi che sia stata eseguita la sincronizzazione dell'hash della password di Azure AD Connect o la reimpostazione della password self-service in modo che l'account sia in grado di accedere al dominio gestito di Azure Active Directory Domain Services.
* Un host di Azure Bastion distribuito nella rete virtuale Azure AD DS.
    * Se necessario, [creare un host Bastion di Azure][azure-bastion].

Se si dispone già di una macchina virtuale da aggiungere a un dominio, passare alla sezione che descrive come [aggiungere la macchina virtuale al dominio gestito di Azure Active Directory Domain Services](#join-the-vm-to-the-azure-ad-ds-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

In questa esercitazione si creerà una macchina virtuale Windows Server da aggiungere al dominio gestito di Azure Active Directory Domain Services usando il portale di Azure. Per iniziare, accedere prima di tutto al [portale di Azure](https://portal.azure.com).

## <a name="create-a-windows-server-virtual-machine"></a>creare una macchina virtuale Windows Server

Per informazioni su come aggiungere un computer a un dominio gestito di Azure Active Directory Domain Services, si creerà una macchina virtuale Windows Server. Questa macchina virtuale è connessa a una rete virtuale di Azure che fornisce la connettività al dominio gestito di Azure Active Directory Domain Services. La procedura per l'aggiunta a un dominio gestito di Azure Active Directory Domain Services è identica a quella per l'aggiunta a un normale dominio di Active Directory Domain Services locale.

Se si dispone già di una macchina virtuale da aggiungere a un dominio, passare alla sezione che descrive come [aggiungere la macchina virtuale al dominio gestito di Azure Active Directory Domain Services](#join-the-vm-to-the-azure-ad-ds-managed-domain).

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.

1. In **Per iniziare** scegliere **Windows Server 2016 Datacenter**.

    ![Scegliere di creare una macchina virtuale Windows Server 2016 Datacenter nel portale di Azure](./media/join-windows-vm/select-vm-image.png)

1. Nella finestra **Nozioni di base** configurare le impostazioni di base per la macchina virtuale. Lasciare invariate le impostazioni predefinite per *Opzioni di disponibilità*, *Immagine* e *Dimensioni*.

    | Parametro            | Valore consigliato   |
    |----------------------|-------------------|
    | Resource group       | Selezionare o creare un gruppo di risorse, ad *esempio myResourceGroup* |
    | Nome macchina virtuale | Immettere un nome per la macchina virtuale, ad esempio *myVM* |
    | Region               | Scegliere l'area in cui creare la macchina virtuale, ad esempio *Stati Uniti orientali* |
    | Username             | Immettere un nome utente per l'account amministratore locale da creare nella macchina virtuale, ad esempio *azureuser* |
    | Password             | Immettere e confermare una password sicura per l'amministratore locale da creare nella macchina virtuale. Non specificare le credenziali di un account utente di dominio. |

1. Per impostazione predefinita, le macchine virtuali create in Azure sono accessibili da Internet tramite RDP. Quando è abilitato il protocollo RDP, è probabile che si verifichino attacchi di accesso automatizzato, che possono disabilitare gli account con nomi comuni come *admin* o *administrator* a causa di più tentativi di accesso successivi non riusciti.

    Il protocollo RDP deve essere abilitato solo quando necessario e limitato a un set di intervalli di indirizzi IP autorizzati. Questa configurazione consente di migliorare la sicurezza della macchina virtuale e di ridurre l'area soggetta a potenziali attacchi. In alternativa, creare e usare un host Bastion di Azure che consenta l'accesso solo tramite il portale di Azure tramite SSL. Nel passaggio successivo di questa esercitazione si userà un host di Azure Bastion per connettersi in modo sicuro alla macchina virtuale.

    Per il momento disabilitare le connessioni RDP dirette alla macchina virtuale.

    In **porte in ingresso pubbliche**selezionare *nessuno*.

1. Al termine, selezionare **Avanti: dischi**.
1. Dal menu a discesa per tipo di **disco del sistema operativo**scegliere *SDD standard*, quindi selezionare **Avanti: rete**.
1. La macchina virtuale deve connettersi a una subnet di rete virtuale di Azure in grado di comunicare con la subnet in cui è distribuito il dominio gestito di Azure Active Directory Domain Services. È consigliabile distribuire un dominio gestito di Azure Active Directory Domain Services nella relativa subnet dedicata. Non distribuire la macchina virtuale nella stessa subnet del dominio gestito di Azure Active Directory Domain Services.

    Esistono due modi principali per distribuire la macchina virtuale e connettersi a una subnet di rete virtuale appropriata:
    
    * Creare una subnet, o selezionarne una esistente, nella stessa rete virtuale in cui viene distribuito il dominio gestito di Azure Active Directory Domain Services.
    * Selezionare una subnet in una rete virtuale di Azure connessa alla subnet specificata nel passaggio precedente tramite [peering di rete virtuale di Azure][vnet-peering].
    
    Se si seleziona una subnet di rete virtuale che non è connessa alla subnet relativa all'istanza di Azure Active Directory Domain Services, non è possibile aggiungere la macchina virtuale al dominio gestito. Per questa esercitazione si creerà una nuova subnet nella rete virtuale di Azure.

    Nel riquadro **Rete** selezionare la rete virtuale in cui è distribuito il dominio gestito di Azure Active Directory Domain Services, ad esempio *aaads-vnet*
1. In questo esempio viene mostrata la subnet *aaads-subnet* esistente a cui è connesso il dominio gestito di Azure Active Directory Domain Services. Non connettere la macchina virtuale a questa subnet. Per creare una subnet per la macchina virtuale, selezionare **Gestisci configurazione subnet**.

    ![Scegliere di gestire la configurazione della subnet nel portale di Azure](./media/join-windows-vm/manage-subnet.png)

1. Nel menu sinistro della finestra della rete virtuale selezionare **Spazio degli indirizzi**. La rete virtuale viene creata con un singolo spazio di indirizzi, *10.0.1.0/24*, usato dalla subnet predefinita.

    Aggiungere un intervallo di indirizzi IP aggiuntivo alla rete virtuale. Le dimensioni di questo intervallo di indirizzi e l'intervallo di indirizzi IP effettivo da usare dipendono dalle altre risorse di rete già distribuite. L'intervallo di indirizzi IP non deve sovrapporsi agli intervalli di indirizzi esistenti nell'ambiente di Azure o in locale. Assicurarsi di scegliere dimensioni dell'intervallo di indirizzi IP sufficienti per il numero di VM che si prevede di distribuire nella subnet.

    Nell'esempio seguente viene aggiunto l'intervallo di indirizzi IP *10.0.2.0/24*. Al termine, selezionare **Salva**.

    ![Aggiungere un altro intervallo di indirizzi IP di rete virtuale nel portale di Azure](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Successivamente, nel menu sinistro della finestra della rete virtuale selezionare **Subnet**, quindi scegliere **+ Subnet** per aggiungere una subnet.

1. Selezionare **+ Subnet** e quindi immettere un nome per la subnet, ad esempio *management*. Specificare un valore in **Intervallo di indirizzi (blocco CIDR)** , ad esempio *10.0.2.0/24*. Assicurarsi che questo intervallo di indirizzi IP non si sovrapponga ad altri intervalli di indirizzi di Azure o locali esistenti. Lasciare invariati i valori predefiniti delle altre opzioni e quindi scegliere **OK**.

    ![Creare una configurazione di subnet nel portale di Azure](./media/join-windows-vm/create-subnet.png)

1. Per la creazione della subnet sono necessari alcuni secondi. Dopo aver creato la subnet, selezionare la *X* per chiudere la finestra.
1. Tornare al riquadro **Rete** per creare una macchina virtuale e scegliere la subnet creata dal menu a discesa, ad esempio *management*. Anche in questo caso, assicurarsi di scegliere la subnet corretta e non distribuire la macchina virtuale nella stessa subnet del dominio gestito di Azure Active Directory Domain Services.
1. Lasciare invariati i valori predefiniti delle altre opzioni e quindi scegliere **Gestione**.
1. Impostare **Diagnostica di avvio** su *Off*. Lasciare invariati i valori predefiniti delle altre opzioni e quindi scegliere **Rivedi e crea**.
1. Rivedere le impostazioni della macchina virtuale e quindi selezionare **Crea**.

La creazione della macchina virtuale richiede alcuni minuti. Il portale di Azure mostra lo stato della distribuzione. Quando la macchina virtuale è pronta, selezionare **Vai alla risorsa**.

![Passare alla risorsa Macchina virtuale nel portale di Azure dopo che è stata creata](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Connettersi alla macchina virtuale Windows Server

Per connettersi in modo sicuro alle macchine virtuali, usare un host Bastion di Azure. Con Azure Bastion, un host gestito viene distribuito nella rete virtuale e fornisce connessioni RDP o SSH basate sul Web alle macchine virtuali. Per le macchine virtuali non sono necessari indirizzi IP pubblici e non è necessario aprire regole del gruppo di sicurezza di rete per il traffico remoto esterno. Ci si connette alle macchine virtuali usando il portale di Azure dal Web browser.

Per usare un host Bastion per connettersi alla VM, completare i passaggi seguenti:

1. Nel riquadro **Panoramica** per la VM selezionare **Connetti**, quindi **Bastion**.

    ![Connettersi a una macchina virtuale Windows usando Bastion nel portale di Azure](./media/join-windows-vm/connect-to-vm.png)

1. Immettere le credenziali per la macchina virtuale specificata nella sezione precedente, quindi selezionare **Connetti**.

   ![Connettersi tramite l'host Bastion nella portale di Azure](./media/join-windows-vm/connect-to-bastion.png)

Se necessario, consentire al Web browser di aprire popup per la visualizzazione della connessione Bastion. Per eseguire la connessione alla macchina virtuale sono necessari alcuni secondi.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>Aggiungere la macchina virtuale al dominio gestito di Azure Active Directory Domain Services

Con la macchina virtuale creata e una connessione RDP basata sul Web stabilita con Azure Bastion, ora è possibile aggiungere la macchina virtuale Windows Server al dominio gestito di Azure AD DS. Questa procedura è identica a quella usata per connettere un computer a un normale dominio di Active Directory Domain Services locale.

1. Se **Server Manager** non viene aperto per impostazione predefinita quando si accede alla macchina virtuale, selezionare il menu **Start** e quindi scegliere **Server Manager**.
1. Nel riquadro sinistro della finestra **Server Manager** selezionare **Local Server**. Nell'area **Proprietà** del riquadro destro scegliere **Gruppo di lavoro**.

    ![Aprire Server Manager nella macchina virtuale e modificare la proprietà del gruppo di lavoro](./media/join-windows-vm/server-manager.png)

1. Nella finestra **Proprietà del sistema** selezionare **Modifica** per aggiungere la macchina virtuale al dominio gestito di Azure Active Directory Domain Services.

    ![Scegliere di modificare le proprietà del gruppo di lavoro o del dominio](./media/join-windows-vm/change-domain.png)

1. Nella casella **dominio** specificare il nome del dominio gestito di Azure AD DS, ad esempio *aaddscontoso.com*, quindi fare clic su **OK**.

    ![Specificare il dominio gestito di Azure Active Directory Domain Services a cui aggiungere la macchina virtuale](./media/join-windows-vm/join-domain.png)

1. Immettere le credenziali di dominio per l'aggiunta al dominio. Usare le credenziali di un utente che fa parte del dominio gestito di Azure AD DS. L'account deve far parte del dominio gestito di Azure AD DS o del tenant di Azure AD. Gli account di directory esterne associate al tenant di Azure AD non possono eseguire correttamente l'autenticazione durante il processo di aggiunta al dominio. Le credenziali dell'account possono essere specificate in uno dei modi seguenti:

    * **Formato UPN** (consigliato): immettere il suffisso UPN (User Principal Name) per l'account utente, come configurato in Azure AD. Ad esempio, il suffisso UPN dell'utente *contosoadmin* sarà `contosoadmin@aaddscontoso.onmicrosoft.com`. Esistono un paio di casi d'uso comuni in cui il formato UPN può essere usato in modo affidabile per accedere al dominio in sostituzione del formato *SAMAccountName*:
        * Se il prefisso UPN di un utente è lungo, ad esempio *nomeutentetroppolungo*, è possibile che venga generato automaticamente il formato *SAMAccountName*.
        * Se più utenti hanno lo stesso prefisso UPN nel tenant di Azure AD, ad esempio *utente*, è possibile che venga generato automaticamente il formato *SAMAccountName*.
    * **Formato SAMAccountName**: immettere il nome dell'account nel formato *SAMAccountName*. Ad esempio, il formato *SAMAccountName* dell'utente *contosoadmin* sarà `AADDSCONTOSO\contosoadmin`.

1. Per l'aggiunta al dominio gestito di Azure Active Directory Domain Services sono necessari alcuni secondi. Al termine dell'operazione, viene visualizzato il seguente messaggio di benvenuto nel dominio:

    ![Messaggio di benvenuto al dominio](./media/join-windows-vm/join-domain-successful.png)

    Selezionare **OK** per continuare.

1. Per completare la procedura di aggiunta al dominio gestito di Azure Active Directory Domain Services, riavviare la macchina virtuale.

> [!TIP]
> È possibile aggiungere una macchina virtuale a un dominio usando PowerShell con il cmdlet [Add-Computer][add-computer]. Nell'esempio seguente viene unito il dominio *AADDSCONTOSO* , quindi viene riavviata la macchina virtuale. Quando richiesto, immettere le credenziali per un utente che fa parte del dominio gestito di Azure AD DS:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> Per aggiungere al dominio una macchina virtuale senza connetterla e configurare manualmente la connessione, è possibile usare il cmdlet [Set-AzVmAdDomainExtension][set-azvmaddomainextension] di Azure PowerShell.

Dopo il riavvio della macchina virtuale Windows Server, i criteri applicati nel dominio gestito di Azure Active Directory Domain Services verranno inseriti tramite push nella macchina virtuale. È ora possibile accedere alla macchina virtuale Windows Server VM anche usando le credenziali di dominio appropriate.

## <a name="clean-up-resources"></a>Pulire le risorse

Nell'esercitazione successiva si userà questa macchina virtuale Windows Server per installare gli strumenti di gestione che consentono di amministrare il dominio gestito di Azure Active Directory Domain Services. Se non si vuole continuare in questa serie di esercitazioni, vedere la procedura di pulizia seguente per [eliminare la macchina virtuale](#delete-the-vm). In caso contrario, [continuare con l'esercitazione successiva](#next-steps).

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>Rimuovere la macchina virtuale dal dominio gestito di Azure Active Directory Domain Services

Per rimuovere la macchina virtuale dal dominio gestito di Azure Active Directory Domain Services, eseguire di nuovo la procedura per [aggiungere la macchina virtuale a un dominio](#join-the-vm-to-the-azure-ad-ds-managed-domain). Invece di aggiungere la macchina virtuale al dominio gestito di Azure Active Directory Domain Services, scegliere di aggiungere un gruppo di lavoro, ad esempio il gruppo di lavoro predefinito *WORKGROUP*. Dopo il riavvio della macchina virtuale, l'oggetto computer viene rimosso dal dominio gestito di Active Directory Domain Services.

Se si [elimina la macchina virtuale](#delete-the-vm) senza rimuoverla dal dominio, in Azure Active Directory Domain Services rimarrà un oggetto computer orfano.

### <a name="delete-the-vm"></a>Eliminare la macchina virtuale

Se non si intende più usare la macchina virtuale Windows Server, eliminarla seguendo questa procedura:

1. Dal menu a sinistra selezionare **Gruppi di risorse**
1. Scegliere il gruppo di risorse, ad esempio *myResourceGroup*.
1. Scegliere la macchina virtuale, ad esempio *myVM*, e quindi selezionare **Elimina**. Selezionare **Sì** per confermare l'eliminazione della risorsa. Per l'eliminazione della macchina virtuale sono necessari alcuni minuti.
1. Dopo aver eliminato la macchina virtuale, selezionare il disco del sistema operativo, la scheda di interfaccia di rete e le eventuali altre risorse con il prefisso *myVM-* ed eliminarle.

## <a name="troubleshoot-domain-join-issues"></a>Risolvere i problemi relativi all'aggiunta al dominio

La macchina virtuale Windows Server dovrebbe essere aggiunta al dominio gestito di Azure Active Directory Domain Services nello stesso modo in cui un normale computer locale viene aggiunto a un dominio di Active Directory Domain Services. Se la macchina virtuale Windows Server non può essere aggiunta al dominio gestito di Azure Active Directory Domain Services, significa che si è verificato un problema di connettività o un problema correlato alle credenziali. Vedere le sezioni di risoluzione dei problemi riportate di seguito per aggiungere correttamente la macchina virtuale al dominio gestito.

### <a name="connectivity-issues"></a>Problemi di connettività

Se non viene visualizzato un messaggio di richiesta di credenziali per l'aggiunta al dominio, significa che si è verificato un problema di connettività. La macchina virtuale non riesce a raggiungere il dominio gestito di Azure Active Directory Domain Services nella rete virtuale.

Dopo aver provato ognuno di questi passaggi di risoluzione dei problemi, provare ad aggiungere di nuovo la macchina virtuale Windows Server al dominio gestito.

* Verificare che la macchina virtuale sia connessa alla stessa rete virtuale in cui è abilitato Azure Active Directory Domain Services oppure che disponga di una connessione di rete con peering.
* Provare a effettuare il ping del nome DNS del dominio gestito, ad esempio `ping aaddscontoso.com`.
    * Se la richiesta ping ha esito negativo, provare a effettuare il ping degli indirizzi IP per il dominio gestito, ad esempio `ping 10.0.0.4`. Quando si seleziona il dominio gestito di Azure Active Directory Domain Services dall'elenco delle risorse di Azure, l'indirizzo IP relativo all'ambiente viene visualizzato nella pagina *Proprietà*.
    * Se si riesce a effettuare il ping dell'indirizzo IP ma non del dominio, la configurazione del server DNS potrebbe non essere valida. Verificare che gli indirizzi IP del dominio gestito siano configurati come server DNS per la rete virtuale.
* Provare a scaricare la cache del resolver DNS sulla macchina virtuale usando il comando `ipconfig /flushdns`.

### <a name="credentials-related-issues"></a>Problemi correlati alle credenziali

Se viene visualizzato un messaggio di richiesta di credenziali per l'aggiunta al dominio, ma successivamente viene restituito un errore dopo che si sono specificate le credenziali, significa che la macchina virtuale riesce a connettersi al dominio gestito di Azure Active Directory Domain Services, ma le credenziali specificate non consentono l'aggiunta della macchina virtuale a tale dominio.

Dopo aver provato ognuno di questi passaggi di risoluzione dei problemi, provare ad aggiungere di nuovo la macchina virtuale Windows Server al dominio gestito.

* Assicurarsi che l'account utente specificato appartenga al dominio gestito di Azure AD DS.
* Verificare che l'account faccia parte del dominio gestito di Azure AD DS o del tenant di Azure AD. Gli account di directory esterne associate al tenant di Azure AD non possono eseguire correttamente l'autenticazione durante il processo di aggiunta al dominio.
* Provare a usare il formato UPN per specificare le credenziali, ad esempio `contosoadmin@aaddscontoso.onmicrosoft.com`. Se sono presenti diversi utenti con lo stesso prefisso UPN nel tenant o se il prefisso UPN è troppo lungo, è possibile che venga generato automaticamente il formato *SAMAccountName*. In questi casi, il formato *SAMAccountName* per l'account può essere diverso da quello previsto o usato nel dominio locale.
* Controllare di avere [abilitato la sincronizzazione password][password-sync] nel dominio gestito. Senza questo passaggio di configurazione, gli hash delle password richiesti non saranno presenti nel dominio gestito di Azure Active Directory Domain Services per autenticare correttamente il tentativo di accesso.
* Attendere il completamento della sincronizzazione delle password. Quando viene cambiata la password di un account utente, una sincronizzazione automatica in background di Azure AD la aggiorna in Azure AD DS. La disponibilità della password per l'aggiunta a un dominio richiede tempo.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare una macchina virtuale Windows Server
> * Connettersi alla macchina virtuale Windows Server in una rete virtuale di Azure
> * Aggiungere la macchina virtuale al dominio gestito di Azure Active Directory Domain Services

Per amministrare il dominio gestito di Azure Active Directory Domain Services, configurare una macchina virtuale di gestione usando il Centro di amministrazione di Active Directory (ADAC).

> [!div class="nextstepaction"]
> [Installare gli strumenti di amministrazione in una macchina virtuale di gestione](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
