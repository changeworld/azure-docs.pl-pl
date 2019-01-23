---
title: Zainstaluj program Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL | Dokumentacja firmy Microsoft
description: W tym temacie opisano aktualizację programu Azure AD Connect, która służy do instalacji przy użyciu konta, które ma tylko uprawnienia dbo programu SQL.
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
ms.topic: article
ms.date: 06/07/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: e4f1e60424e058f805e89fc3291cfa9a28390c8d
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474917"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Instalowanie programu Azure AD Connect przy użyciu języka SQL delegowane uprawnienia administratora
Przed najnowszej kompilacji program Azure AD Connect administracyjne delegowania, podczas wdrażania konfiguracje, które wymagały SQL, nie jest obsługiwana.  Użytkownicy, którzy chcieli zainstalować program Azure AD Connect potrzebne musi mieć uprawnienia administratora serwera w programie SQL server.

Za pomocą najnowszej wersji programu Azure AD Connect teraz inicjowania obsługi usługi bazy danych mogą być wykonywane poza pasmem administrator usługi SQL, a następnie przez administratora usługi Azure AD Connect z uprawnieniami właściciela bazy danych.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby użyć tej funkcji, należy weź pod uwagę, że istnieje kilka ruchomych części, a każdy z nich może obejmować innego administratora w Twojej organizacji.  W poniższej tabeli podsumowano poszczególnych ról i obowiązków odpowiednich we wdrażaniu usługi Azure AD Connect przy użyciu tej funkcji.

|Rola|Opis|
|-----|-----|
|Domeny lub lasu, administrator usługi AD|Tworzy konto usługi na poziomie domeny, który jest używany przez program Azure AD Connect do uruchamiania usługi synchronizacji.  Aby uzyskać więcej informacji na temat kont usług, zobacz [konta i uprawnienia](reference-connect-accounts-permissions.md).
|Administratora SQL|Proces tworzenia bazy danych ADSync, a następnie przyznaje logowania + dbo dostęp do administratora usługi Azure AD Connect i konto usługi utworzone przez administratora domeny lub lesie.|
Administrator usługi Azure AD Connect|Instaluje program Azure AD Connect i określa konto usługi podczas instalacji niestandardowej.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Kroki wymagane do zainstalowania Azure AD Connect przy użyciu języka SQL delegowane uprawnienia
Aby aprowizować bazę danych poza pasmem i zainstalować program Azure AD Connect z uprawnieniami właściciela bazy danych, wykonaj następujące kroki.

>[!NOTE]
>Chociaż nie jest wymagane, jest **zdecydowanie zaleca się** czy sortowania Latin1_General_CI_AS wybrano podczas tworzenia bazy danych.


1.  Tworzenie bazy danych ADSync sekwencji sortowania bez uwzględniania wielkości liter administratora SQL **(Latin1_General_CI_AS)**.  Baza danych musi mieć nazwę **ADSync**.  Model odzyskiwania, poziom zgodności i typ relacji zawierania są aktualizowane poprawne wartości, gdy jest zainstalowany program Azure AD Connect.  Jednak sekwencji sortowania muszą być ustawione poprawnie przez administratora SQL w przeciwnym razie program Azure AD Connect spowoduje zablokowanie instalacji.  Aby odzyskać skojarzenia zabezpieczeń musi usunięcie i ponowne utworzenie bazy danych.</br>
![Sortowanie](./media/how-to-connect-install-sql-delegation/sql4.png)
2.  Przyznaj administrator usługi Azure AD Connect i konta usług domeny następujące uprawnienia:
    - Identyfikator logowania SQL 
    - **Baza danych owner(dbo)** praw.  </br>
![Uprawnienia](./media/how-to-connect-install-sql-delegation/sql3a.png)
3.  Wyślij wiadomość e-mail do administratora usługi Azure AD Connect, wskazującą nazwę programu SQL serwer i wystąpienie, które mają być używane podczas instalowania usługi Azure AD Connect.

## <a name="additional-information"></a>Dodatkowe informacje
Po zaaprowizowaniu bazy danych, administrator usługi Azure AD Connect zainstalować i skonfigurować synchronizację lokalnych własnego uznania.  

**/Useexistingdatabase** flaga jest wymagane w przypadku, gdy przy użyciu wstępnie utworzonej bazy danych.  Jest ono nie używane wyłącznie w sytuacji odzyskiwania.

Oprócz obsługi nowych instalacji programu Azure AD Connect, ta funkcja umożliwia także delegowania dla dowolnych scenariuszy związanych z **/useexistingdatabase** flagi.  Aby uzyskać więcej informacji na temat instalowania usługi Azure AD Connect przy użyciu istniejącej bazy danych, zobacz [zainstalować program Azure AD Connect przy użyciu istniejącej bazy danych ADSync](how-to-connect-install-existing-database.md)


## <a name="next-steps"></a>Kolejne kroki
- [Rozpoczynanie pracy z programem Azure AD Connect przy użyciu ustawień ekspresowych](how-to-connect-install-express.md)
- [Niestandardowa instalacja programu Azure AD Connect](how-to-connect-install-custom.md)
- [Instalowanie programu Azure AD Connect przy użyciu istniejącej bazy danych programu ADSync](how-to-connect-install-existing-database.md)  
