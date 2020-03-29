---
title: Instalowanie usługi Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL | Dokumenty firmy Microsoft
description: W tym temacie opisano aktualizację usługi Azure AD Connect, która umożliwia instalację przy użyciu konta, które ma tylko uprawnienia dbo SQL.
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6269d00c9a6a8f827a4e31044d9d20efb0f8471b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60243550"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Instalowanie programu Azure AD Connect za pomocą delegowanych uprawnień administratora usługi SQL
Przed najnowszą kompilacją usługi Azure AD Connect delegowanie administracyjne podczas wdrażania konfiguracji wymaganych sql nie było obsługiwane.  Użytkownicy, którzy chcieli zainstalować usługę Azure AD Connect, muszą mieć uprawnienia administratora serwera (SA) na serwerze SQL.

W najnowszej wersji usługi Azure AD Connect aprowizacji bazy danych mogą być teraz wykonywane poza pasmem przez administratora SQL, a następnie instalowane przez administratora usługi Azure AD Connect z prawami właściciela bazy danych.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby korzystać z tej funkcji, należy zdać sobie sprawę, że istnieje kilka ruchomych części i każda z nich może obejmować innego administratora w organizacji.  W poniższej tabeli podsumowano poszczególne role i ich odpowiednie obowiązki podczas wdrażania usługi Azure AD Connect za pomocą tej funkcji.

|Rola|Opis|
|-----|-----|
|Administrator domeny lub usługi Forest AD|Tworzy konto usługi na poziomie domeny, które jest używane przez usługę Azure AD Connect do uruchamiania usługi synchronizacji.  Aby uzyskać więcej informacji na temat kont usług, zobacz [Konta i uprawnienia](reference-connect-accounts-permissions.md).
|Administrator SQL|Tworzy bazę danych ADSync i udziela dostępu logowania + dbo do administratora usługi Azure AD Connect i konta usługi utworzonego przez administratora domeny/lasu.|
Administrator usługi Azure AD Connect|Instaluje usługę Azure AD Connect i określa konto usługi podczas instalacji niestandardowej.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Kroki instalowania usługi Azure AD Connect przy użyciu uprawnień delegowanych SQL
Aby aprowizować bazę danych poza pasmem i zainstalować usługę Azure AD Connect z uprawnieniami właściciela bazy danych, należy wykonać następujące kroki.

>[!NOTE]
>Chociaż nie jest to wymagane, **zdecydowanie zaleca się,** że sortowanie Latin1_General_CI_AS jest zaznaczone podczas tworzenia bazy danych.


1. Niech administrator SQL utworzy bazę danych ADSync z sekwencją sortowania bez uwzględniania wielkości liter **(Latin1_General_CI_AS).**  Baza danych musi mieć nazwę **ADSync**.  Model odzyskiwania, poziom zgodności i typ zamknięcia są aktualizowane do prawidłowych wartości po zainstalowaniu usługi Azure AD Connect.  Jednak sekwencja sortowania musi być poprawnie ustawiona przez administratora SQL, w przeciwnym razie usługa Azure AD Connect zablokuje instalację.  Aby odzyskać sa należy usunąć i ponownie utworzyć bazę danych.
 
   ![Sortowanie](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Udziel administratorowi usługi Azure AD Connect i kontu usługi domeny następujących uprawnień:
   - Identyfikator logowania SQL 
   - **właściciel bazy danych(dbo)** prawa.
 
   ![Uprawnienia](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Usługa Azure AD Connect nie obsługuje logowania z zagnieżdżoną subskrypcją.  Oznacza to, że konto administratora usługi Azure AD Connect i konto usługi domeny muszą być połączone z identyfikatorem logowania, któremu przyznano prawa dbo.  Nie może być po prostu członkiem grupy, która jest przypisana do logowania z dbo praw.

3. Wyślij wiadomość e-mail do administratora usługi Azure AD Connect wskazującą nazwę serwera SQL i wystąpienia, które powinny być używane podczas instalowania usługi Azure AD Connect.

## <a name="additional-information"></a>Dodatkowe informacje
Po zainicjowaniu obsługi administracyjnej bazy danych administrator usługi Azure AD Connect może instalować i konfigurować synchronizację lokalną w dogodnym dla nich momencie.

W przypadku, gdy administrator SQL przywrócił bazę danych USŁUGI ADSync z poprzedniej kopii zapasowej usługi Azure AD Connect, należy zainstalować nowy serwer usługi Azure AD Connect przy użyciu istniejącej bazy danych. Aby uzyskać więcej informacji na temat instalowania usługi Azure AD Connect z istniejącą bazą danych, zobacz [Instalowanie usługi Azure AD Connect przy użyciu istniejącej bazy danych USŁUGI ADSync](how-to-connect-install-existing-database.md).

## <a name="next-steps"></a>Następne kroki
- [Rozpoczynanie pracy z programem Azure AD Connect przy użyciu ustawień ekspresowych](how-to-connect-install-express.md)
- [Niestandardowa instalacja programu Azure AD Connect](how-to-connect-install-custom.md)
- [Instalowanie programu Azure AD Connect przy użyciu istniejącej bazy danych programu ADSync](how-to-connect-install-existing-database.md)  
