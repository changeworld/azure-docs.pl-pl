---
title: Połącz do usług Azure Analysis Services przy użyciu programu Excel | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć się z serwerem usług Analysis Services dla platformy Azure przy użyciu programu Excel.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6585648cbb461f52a22bc6ea239c0899caa2c2e7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597267"
---
# <a name="connect-with-excel"></a>Łączenie z programem Excel

Po utworzeniu serwera i wdrożone modelu tabelarycznego, klienci mogą połączyć i rozpocząć eksplorowanie danych. 

## <a name="before-you-begin"></a>Przed rozpoczęciem
Konto, które możesz zalogować się przy użyciu musi należeć do roli bazy danych modelu z co najmniej uprawnienia do odczytu. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Łączenie programu Excel

Łączenie z serwerem w programie Excel jest obsługiwana przy użyciu pobieranie danych w programie Excel 2016. Łączenie za pomocą Kreatora importu tabeli w dodatku Power Pivot nie jest obsługiwane. 

**Aby połączyć w programie Excel 2016**

1. W programie Excel 2016, w **danych** wstążki, kliknij przycisk **Pobierz dane zewnętrzne** > **z innych źródeł** > **usług Analysis Services**.

2. W Kreatorze połączenia danych w **nazwy serwera**, wprowadź nazwę serwera, łącznie z protokołem i identyfikatora URI. Na przykład asazure://westcentralus.asazure.windows.net/advworks. Następnie w **poświadczeń logowania**, wybierz pozycję **poniższe nazwy użytkownika i hasła**, a następnie wpisz nazwę organizacji użytkownika, na przykład nancy@adventureworks.comi hasło.

    > [!IMPORTANT]
    > Zaloguj się przy użyciu Account Microsoft Live ID, Yahoo, Gmail, itp., czy są wymagane, aby zalogować się przy użyciu usługi Multi-Factor authentication, pole jest puste hasło. Zostanie wyświetlony monit o podanie hasła po kliknięciu przycisku Dalej. 

    ![Nawiązywanie połączenia z logowania programu Excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. W **wybierz bazę danych i tabeli**, wybierz bazę danych i modelu lub perspektywy, a następnie kliknij przycisk **Zakończ**.
   
    ![Nawiązywanie połączenia z wybierz model programu Excel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Zobacz także
[Biblioteki klienta](analysis-services-data-providers.md)   
[Zarządzanie serwerem](analysis-services-manage.md)     


