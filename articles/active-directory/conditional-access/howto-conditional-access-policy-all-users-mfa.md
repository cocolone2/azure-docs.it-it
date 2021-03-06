---
title: "Accesso condizionale: richiedere l'autenticazione a più fattori per tutti gli utenti-Azure Active Directory"
description: Creare un criterio di accesso condizionale personalizzato per richiedere a tutti gli utenti di eseguire l'autenticazione a più fattori
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52faa2b6167606a46bf189d514a1eb314b443783
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424929"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Accesso condizionale: richiedere l'autenticazione a più fattori per tutti gli utenti

Come Alex Weinert, la directory di Identity Security di Microsoft, cita nel suo Blog il post di Blog della [PA $ $Word non è rilevante](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984):

> La password non è rilevante, ma l'autenticazione a più fattori. In base ai nostri studi, il tuo account è più del 99,9% meno probabile che venga compromesso se usi l'autenticazione a più fattori.

Le indicazioni fornite in questo articolo consentiranno all'organizzazione di creare criteri di autenticazione a più fattori bilanciati per l'ambiente.

## <a name="user-exclusions"></a>Esclusioni utente

I criteri di accesso condizionale sono strumenti avanzati, quindi è consigliabile escludere dal criterio i seguenti account:

* Account di **accesso di emergenza** o **break-Glass** per impedire il blocco degli account a livello di tenant. Nello scenario improbabile che tutti gli amministratori siano bloccati dal tenant, è possibile usare l'account amministrativo di accesso di emergenza per accedere al tenant per recuperare l'accesso.
   * Altre informazioni sono disponibili nell'articolo [gestire gli account di accesso di emergenza in Azure ad](../users-groups-roles/directory-emergency-access.md).
* Gli **account del servizio** e i principi di **servizio**, ad esempio l'account Azure ad Connect Sync. Gli account del servizio sono account non interattivi che non sono collegati a un utente specifico. Vengono in genere usati dai servizi back-end e consentono l'accesso a livello di codice alle applicazioni. Gli account del servizio devono essere esclusi perché non è possibile completare l'autenticazione a livello di codice.
   * Se l'organizzazione dispone di questi account in uso negli script o nel codice, è consigliabile sostituirli con [identità gestite](../managed-identities-azure-resources/overview.md). Come soluzione temporanea, è possibile escludere questi account specifici dai criteri di base.

## <a name="application-exclusions"></a>Esclusioni delle applicazioni

Le organizzazioni possono avere molte applicazioni cloud in uso. Non tutte le applicazioni possono richiedere la stessa sicurezza. Ad esempio, le applicazioni per le retribuzioni e la partecipazione possono richiedere l'autenticazione a più fattori, ma la caffetteria probabilmente no. Gli amministratori possono scegliere di escludere applicazioni specifiche dal criterio.

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

La procedura seguente consente di creare un criterio di accesso condizionale per richiedere che i ruoli amministrativi assegnati eseguano la funzionalità di autenticazione a più fattori.

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare a **Azure Active Directory** **sicurezza** >  > **l'accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **assegnazioni**selezionare **utenti e gruppi** .
   1. In **Includi**selezionare **tutti gli utenti**
   1. In **Escludi**selezionare **utenti e gruppi** e scegliere l'accesso di emergenza dell'organizzazione o gli account break-Glass. 
   1. Selezionare **Operazione completata**.
1. In **app Cloud o azioni** > **Includi**Selezionare **tutte le app Cloud**.
   1. In **Escludi**selezionare le applicazioni che non richiedono l'autenticazione a più fattori.
1. In **controlli di accesso** > **concedere**Selezionare **Concedi accesso**, **Richiedi autenticazione**a più fattori e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** **su on**.
1. Selezionare **Crea** per creare per abilitare i criteri.

### <a name="named-locations"></a>Posizioni specifiche

Le organizzazioni possono scegliere di incorporare i percorsi di rete noti, detti **località denominate** , ai criteri di accesso condizionale. Queste località denominate possono includere reti IPv4 attendibili come quelle per una sede centrale. Per ulteriori informazioni sulla configurazione delle località denominate, vedere l'articolo [Qual è la condizione di posizione in Azure Active Directory l'accesso condizionale?](location-condition.md)

Nei criteri di esempio precedenti, un'organizzazione può scegliere di non richiedere l'autenticazione a più fattori per l'accesso a un'app Cloud dalla propria rete aziendale. In questo caso, è possibile aggiungere la configurazione seguente ai criteri:

1. In **assegnazioni**selezionare **condizioni** > **percorsi**.
   1. Configurare **Sì**.
   1. Includere **qualsiasi percorso**.
   1. Escludere **tutti i percorsi attendibili**.
   1. Selezionare **Operazione completata**.
1. Selezionare **Operazione completata**.
1. **Salvare** le modifiche apportate ai criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

[Determinare l'effetto usando la modalità solo report di accesso condizionale](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso usando lo strumento di What If dell'accesso condizionale](troubleshoot-conditional-access-what-if.md)
