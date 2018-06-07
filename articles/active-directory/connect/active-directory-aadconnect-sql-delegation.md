---
title: Zainstaluj usługi Azure AD Connect, korzystając z uprawnień administratora delegowanego SQL | Dokumentacja firmy Microsoft
description: W tym temacie opisano aktualizację do usługi Azure AD Connect, która służy do instalacji, używając konta, które ma tylko uprawnienia dbo programu SQL.
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: d0cfce1dfe6482397e421d85d59494e6d75cc441
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591161"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Instalowanie programu Azure AD Connect przy użyciu SQL delegowane uprawnienia administratora
Przed ostatniej kompilacji Azure AD Connect administracyjne delegowania, wdrażając konfiguracje, które wymagane SQL, nie jest obsługiwana.  Użytkownicy, którzy chcieli zainstalować program Azure AD Connect potrzebne do mają uprawnienia administratora serwera na serwerze SQL.

Najnowsza wersja programu Azure AD Connect teraz udostępniania bazy danych może być wykonywane poza pasmem przez administratora programu SQL, a następnie przez administratora programu Azure AD Connect z uprawnień właściciela bazy danych.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby użyć tej funkcji, musisz należy pamiętać, że istnieje kilka części ruchu i każdy z nich może obejmować różne administratorem w Twojej organizacji.  W poniższej tabeli przedstawiono poszczególnych ról i obowiązków odpowiednich wdrażania usługi Azure AD Connect przy użyciu tej funkcji.

|Rola|Opis|
|-----|-----|
|Domeny lub lasu AD administratora|Tworzy konto usługi poziomu domeny, który jest używany przez program Azure AD Connect do uruchamiania usługi synchronizacji.  Aby uzyskać więcej informacji o kontach usług, zobacz [konta i uprawnienia](active-directory-aadconnect-accounts-permissions.md).
|SQL administrator|Tworzy bazę danych ADSync i przydziela logowania + dbo dostęp do administratora programu Azure AD Connect i konto usługi utworzonych przez administratora domeny lub lesie.|
Administrator usługi Azure AD Connect|Instaluje usługi Azure AD Connect i umożliwia określenie konta usługi podczas instalacji niestandardowej.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Kroki wymagane do zainstalowania usługi Azure AD Connect przy użyciu programu SQL delegowane uprawnienia
Do udostępniania bazy danych poza pasmem i instalowanie Azure AD Connect przy użyciu uprawnień właściciela bazy danych, wykonaj następujące kroki.

>[!NOTE]
>Chociaż nie jest wymagana, jest **zdecydowanie zalecane** czy sortowania Latin1_General_CI_AS wybrano podczas tworzenia bazy danych.


1.  Poproś administratora SQL, utworzyć bazę danych ADSync sekwencji sortowania bez uwzględniania wielkości liter **(Latin1_General_CI_AS)**.  Bazy danych musi mieć nazwę **ADSync**.  Model odzyskiwania, poziom zgodności i typ relacji zawierania są aktualizowane poprawną Azure AD Connect jest zainstalowany.  Jednak sekwencji sortowania muszą być ustawione poprawnie przez administratora SQL w przeciwnym razie Azure AD Connect będzie blokował instalacji.  Odzyskiwanie skojarzenia zabezpieczeń należy usunąć i ponownie utworzyć bazy danych.</br>
![Sortowanie](media/active-directory-aadconnect-sql-delegation/sql4.png)
2.  Przyznaj administrator usługi Azure AD Connect i konto usługi domeny następujące uprawnienia:
    - Identyfikator logowania SQL 
    - **Baza danych owner(dbo)** praw.  </br>
![Uprawnienia](media/active-directory-aadconnect-sql-delegation/sql3.png)
3.  Wysłanie wiadomości e-mail do administratora programu Azure AD Connect wskazujący nazwę SQL serwer i wystąpienie, które mają być używane podczas instalacji programu Azure AD Connect.

## <a name="additional-information"></a>Dodatkowe informacje
Po zainicjowaniu obsługi bazy danych, administrator usługi Azure AD Connect można zainstalować i skonfigurować synchronizację lokalnej ich wygodne.  

**/UseExistingDatabase** flaga jest wymagany, gdy przy użyciu wstępnie utworzonej bazy danych.  Go tylko nieużywane w sytuacji odzyskiwania.

Oprócz obsługi nowych instalacji programu Azure AD Connect, ta funkcja umożliwia także delegowania związanej z żadnym scenariuszu **/UseExistingDatabase** flagi.  Aby uzyskać więcej informacji na temat instalowania Azure AD Connect z istniejącej bazy danych, zobacz [instalowania programu Azure AD Connect przy użyciu istniejącej bazy danych ADSync](active-directory-aadconnect-existing-database.md)


## <a name="next-steps"></a>Kolejne kroki
- [Rozpoczynanie pracy z programem Azure AD Connect przy użyciu ustawień ekspresowych](active-directory-aadconnect-get-started-express.md)
- [Niestandardowa instalacja programu Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
- [Zainstalować program Azure AD Connect przy użyciu istniejącej bazy danych ADSync](active-directory-aadconnect-existing-database.md)  
