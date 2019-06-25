---
title: Jak rozwiązywać problemy z usługi Azure Data Catalog
description: W tym artykule opisano typowe zagadnienia dotyczące rozwiązywania problemów dla zasobów usługi Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.openlocfilehash: ed74e90e5e8ed55b75968f51cb50e6a1b4cdd75d
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203504"
---
# <a name="troubleshooting-azure-data-catalog"></a>Rozwiązywanie problemów z usługi Azure Data Catalog

W tym artykule opisano typowe zagadnienia dotyczące rozwiązywania problemów dla zasobów usługi Azure Data Catalog. 

## <a name="functionality-limitations"></a>Ograniczenia dotyczące funkcji

Korzystając z usługi Azure Data Catalog, wynosi następujące funkcje:

- Konta typu **roli gości** nie są obsługiwane. Nie można dodać konta gościa jako użytkownicy usługi Azure Data Catalog, a użytkownicy-goście nie mogą korzystać z portalu na www.azuredatacatalog.com.

- Tworzenie zasobów usługi Azure Data Catalog przy użyciu szablonów usługi Azure Resource Manager lub programu Azure PowerShell polecenia nie jest obsługiwane.

- Nie można przenieść zasobu usługi Azure Data Catalog między dzierżawami usługi Azure.

## <a name="azure-active-directory-policy-configuration"></a>Konfiguracja zasad usługi Azure Active Directory

W niektórych sytuacjach po zalogowaniu się w portalu usługi Azure Data Catalog przy próbie logowania się za pomocą narzędzia do rejestracji źródła danych występuje komunikat o błędzie, który uniemożliwia logowanie. Ten błąd może wystąpić, gdy znajdują się w sieci firmowej lub gdy łączysz się z spoza sieci firmowej.

Narzędzie rejestracji używa *uwierzytelniania formularzy* do weryfikowania logowania użytkowników w usłudze Azure Active Directory. Aby logowanie się powiodło, administrator usługi Azure Active Directory musi włączyć uwierzytelnianie formularzy za pomocą *globalnych zasad uwierzytelniania*.

Pozwoli to na włączenie uwierzytelniania oddzielnie dla połączeń intranetowych i ekstranetowych, jak pokazano na poniższej ilustracji. Błędy logowania mogą wystąpić, jeśli uwierzytelnianie formularzy nie jest włączona dla sieci, z którego jest nawiązywane połączenie.

 ![Globalne zasady uwierzytelniania usługi Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Aby uzyskać więcej informacji, zobacz artykuł [Konfigurowanie zasad uwierzytelniania](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie usługi Azure Data Catalog](data-catalog-get-started.md)
