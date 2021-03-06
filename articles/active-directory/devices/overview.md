---
title: Informazioni sulle identità dei dispositivi in Azure Active Directory
description: Informazioni su come le identità dei dispositivi semplificano la gestione dei dispositivi che accedono alle risorse nell'ambiente in uso.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 594f0ed55b5ce5c31e87fd2011f3bc1522a12380
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378291"
---
# <a name="what-is-a-device-identity"></a>Informazioni sulle identità dei dispositivi

Con la diffusione di dispositivi di tutte le forme e dimensioni e del concetto BYOD (Bring Your Own Device) i professionisti IT si trovano a dover affrontare due obiettivi opposti:

- Consentire agli utenti finali di essere produttivi sempre e ovunque
- Proteggere gli asset dell'organizzazione

Per proteggere questi asset, il personale IT deve gestire prima di tutto le identità dei dispositivi. A tale scopo, può usare strumenti come Microsoft Intune per assicurarsi che siano soddisfatti gli standard di sicurezza e conformità. Azure Active Directory (Azure AD) consente l'accesso Single Sign-On a dispositivi, app e servizi da qualsiasi posizione.

- Gli utenti hanno accesso agli asset dell'organizzazione di cui hanno bisogno. 
- Il personale IT dispone dei controlli necessari per proteggere l'organizzazione.

La gestione delle identità dei dispositivi costituisce un elemento fondamentale per l'[accesso condizionale basato su dispositivo](../conditional-access/require-managed-devices.md). Con i criteri di accesso condizionale basato su dispositivo, è possibile assicurarsi che l'accesso alle risorse nell'ambiente in uso sia possibile solo con dispositivi gestiti.

## <a name="getting-devices-in-azure-ad"></a>Inserire i dispositivi in Azure AD

Per inserire un dispositivo in Azure AD, sono disponibili più opzioni:

- **Registrazione in Azure AD**
   - I dispositivi Azure AD registrati sono in genere di proprietà personale o di dispositivi mobili e hanno eseguito l'accesso con un account Microsoft personale o un altro account locale.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Aggiunta ad Azure AD**
   - I dispositivi Azure AD aggiunti sono di proprietà di un'organizzazione e hanno eseguito l'accesso con un account Azure AD appartenente all'organizzazione. Sono presenti solo nel cloud.
      - Windows 10 
- **Aggiunto a Azure AD in modalità ibrida**
   - I dispositivi ibridi Azure AD aggiunti sono di proprietà di un'organizzazione e hanno eseguito l'accesso con un account Azure AD appartenente all'organizzazione. Sono presenti nel cloud e nell'ambiente locale.
      - Windows 7, 8.1 o 10
      - Windows Server 2008 o versioni successive

![Dispositivi visualizzati nel pannello Dispositivi di Azure AD](./media/overview/azure-active-directory-devices-all-devices.png)

## <a name="device-management"></a>Gestione dei dispositivi

In Azure AD i dispositivi possono essere gestiti usando strumenti per la gestione di dispositivi mobili (MDM), come Microsoft Intune, Microsoft Endpoint Configuration Manager, Criteri di gruppo (aggiunta ad Azure AD in modalità ibrida), strumenti per la gestione di applicazioni mobili (MAM) o altri strumenti di terze parti.

## <a name="resource-access"></a>Accesso alle risorse

La registrazione e l'aggiunta di dispositivi ad Azure AD offrono agli utenti un accesso SSO facile alle risorse cloud. Questo processo consente anche agli amministratori di applicare criteri di accesso condizionale alle risorse in base al dispositivo da cui viene eseguito l'accesso. 

> [!NOTE]
> I criteri di accesso condizionale basati sul dispositivo richiedono dispositivi aggiunti ad Azure AD ibrido oppure dispositivi conformi registrati in Azure AD o aggiunti ad Azure AD.

I dispositivi aggiunti ad Azure AD, o ad Azure AD in modalità ibrida, usufruiscono dell'accesso SSO alle risorse locali dell'organizzazione e alle risorse del cloud. Altre informazioni sono disponibili nell'articolo [Funzionamento dell'accesso SSO alle risorse locali nei dispositivi aggiunti ad Azure AD](azuread-join-sso.md).

## <a name="device-security"></a>Sicurezza dei dispositivi

- I **dispositivi registrati in Azure AD** usano un account gestito dall'utente finale, che può essere un account Microsoft o un'altra credenziale gestita in locale e protetta con almeno una delle tecnologie seguenti.
   - Password
   - PIN
   - Modello
   - Windows Hello
- I **dispositivi aggiunti ad Azure AD o ad Azure AD in modalità ibrida** usano un account aziendale in Azure AD protetto con almeno una delle tecnologie seguenti.
   - Password
   - Windows Hello for Business

## <a name="provisioning"></a>Provisioning

L'inserimento di dispositivi in Azure AD può essere eseguito in modalità self-service o tramite un processo di provisioning controllato dagli amministratori.

## <a name="summary"></a>Summary

Con la gestione delle identità dei dispositivi in Azure AD, è possibile:

- Semplificare il processo di inserimento e gestione dei dispositivi in Azure AD
- Fornire agli utenti un accesso facile da usare alle risorse basate sul cloud dell'organizzazione

## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passaggi successivi

- Vedere altre informazioni sui [dispositivi registrati in Azure AD](concept-azure-ad-register.md)
- Vedere altre informazioni sui [dispositivi aggiunti ad Azure AD](concept-azure-ad-join.md)
- Vedere altre informazioni sui [dispositivi aggiunti ad Azure AD in modalità ibrida](concept-azure-ad-join-hybrid.md)
- Per una panoramica sulla gestione delle identità dei dispositivi nel portale di Azure, vedere [Managing device identities using the Azure portal](device-management-azure-portal.md) (Gestione delle identità dei dispositivi tramite il portale di Azure).
- Per altre informazioni sull'accesso condizionale basato su dispositivo, vedere [Configurare i criteri di accesso condizionale basato su dispositivo di Azure Active Directory](../conditional-access/require-managed-devices.md).
