---
title: Jak rozwiązywać problemy z wykazem danych platformy Azure
description: W tym artykule opisano typowe problemy z rozwiązywaniem problemów z zasobami usługi Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 84bd14f8ae18527b4f6e9d8509a12555baec8771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68879551"
---
# <a name="troubleshooting-azure-data-catalog"></a>Rozwiązywanie problemów z usługą Azure Data Catalog

W tym artykule opisano typowe problemy z rozwiązywaniem problemów z zasobami usługi Azure Data Catalog. 

## <a name="functionality-limitations"></a>Ograniczenia funkcjonalności

Podczas korzystania z usługi Azure Data Catalog następujące funkcje są ograniczone:

- Konta z **typem Guest Role** nie są obsługiwane. Nie można dodawać kont gości jako użytkowników usługi Azure Data [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com)Catalog, a użytkownicy-goście nie mogą korzystać z portalu w pliku .

- Tworzenie zasobów usługi Azure Data Catalog przy użyciu szablonów usługi Azure Resource Manager lub poleceń programu Azure PowerShell nie jest obsługiwane.

- Nie można przenosić zasobu usługi Azure Data Catalog między dzierżawami platformy Azure.

## <a name="azure-active-directory-policy-configuration"></a>Konfiguracja zasad usługi Azure Active Directory

W niektórych sytuacjach po zalogowaniu się w portalu usługi Azure Data Catalog przy próbie logowania się za pomocą narzędzia do rejestracji źródła danych występuje komunikat o błędzie, który uniemożliwia logowanie. Ten błąd może wystąpić, gdy użytkownik jest w sieci firmowej lub gdy łączysz się spoza sieci firmowej.

Narzędzie rejestracji używa *uwierzytelniania formularzy* do weryfikowania logowania użytkowników w usłudze Azure Active Directory. Aby logowanie się powiodło, administrator usługi Azure Active Directory musi włączyć uwierzytelnianie formularzy za pomocą *globalnych zasad uwierzytelniania*.

Pozwoli to na włączenie uwierzytelniania oddzielnie dla połączeń intranetowych i ekstranetowych, jak pokazano na poniższej ilustracji. Jeśli uwierzytelnianie formularzy nie jest włączone dla sieci, z której nawiązujesz połączenie, mogą wystąpić błędy logowania.

 ![Globalne zasady uwierzytelniania usługi Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Aby uzyskać więcej informacji, zobacz artykuł [Konfigurowanie zasad uwierzytelniania](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie usługi Azure Data Catalog](data-catalog-get-started.md)
