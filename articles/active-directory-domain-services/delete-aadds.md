---
title: Usuwanie Usług domenowych Active Directory platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak wyłączyć lub usunąć domenę zarządzaną Usług domenowych Usługi active directory platformy Azure przy użyciu witryny Azure portal
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: e1836f91b8afc1bb4f5b7e141949f3724c57c857
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614038"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Usuwanie domeny zarządzanej usług domenowych usługi active directory platformy Azure przy użyciu portalu Azure

Jeśli nie potrzebujesz już domeny zarządzanej, możesz usunąć wystąpienie usług domenowych Usługi domenowe Active Directory platformy Azure (Usługi Azure AD DS). Nie ma opcji, aby wyłączyć lub tymczasowo wyłączyć domenę zarządzaną usługą Azure AD DS. Usunięcie domeny zarządzanej usług Azure AD DS nie powoduje usunięcia ani w inny sposób niekorzystnego wpływu na dzierżawę usługi Azure AD. W tym artykule pokazano, jak za pomocą witryny Azure Portal, aby usunąć domenę zarządzaną usługą Azure AD DS.

> [!WARNING]
> **Usunięcie jest trwałe i nie można go cofnąć.**
> Po usunięciu domeny zarządzanej usług Azure AD DS występują następujące kroki:
>   * Kontrolery domeny dla domeny zarządzanej są usuwane z sieci wirtualnej i usuwane z niej.
>   * Dane w domenie zarządzanej są trwale usuwane. Te dane obejmują niestandardowe jednostki organizacyjne, niestandardowe rekordy DNS, jednostki usługi, utworzone gmsa itp.
>   * Maszyny przyłączone do domeny zarządzanej tracą relację zaufania z domeną i muszą zostać odłączone od domeny.
>       * Nie można zalogować się do tych komputerów przy użyciu firmowych poświadczeń usługi AD. Zamiast tego należy użyć poświadczeń administratora lokalnego dla komputera.

## <a name="delete-the-managed-domain"></a>Usuwanie domeny zarządzanej

Aby usunąć domenę zarządzaną usługą Azure AD DS, wykonaj następujące kroki:

1. W witrynie Azure portal wyszukaj i wybierz pozycję **Usługi domenowe usługi Azure AD**.
1. Wybierz nazwę domeny zarządzanej usług Azure AD DS, na przykład *aaddscontoso.com*.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**. Aby potwierdzić usunięcie, ponownie wpisz nazwę domeny zarządzanej, a następnie wybierz pozycję **Usuń**.

Usunięcie domeny zarządzanej usług Azure AD DS może potrwać co najmniej 15–20 minut.

## <a name="next-steps"></a>Następne kroki

Należy rozważyć [udostępnienie opinii][feedback] na temat funkcji, które chcesz zobaczyć w usłudze Azure AD DS.

Jeśli chcesz ponownie rozpocząć pracę z usługą Azure AD DS, zobacz [Tworzenie i konfigurowanie wystąpienia usług domenowych Active Directory platformy Azure][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
