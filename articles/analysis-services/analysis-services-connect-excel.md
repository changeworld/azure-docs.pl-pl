---
title: Łączenie z usługami Azure Analysis Services przy użyciu programu Excel | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć się z serwerem usług Azure Analysis Services przy użyciu programu Excel.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5c46d4e4d23744cf07ccf7857a33990bf405a6a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023276"
---
# <a name="connect-with-excel"></a>Łączenie z programem Excel

Po utworzeniu serwera i wdrożyć model tabelaryczny, klienci mogą połączyć i rozpoczęciu eksplorowania danych. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Zaloguj się przy użyciu konta muszą należeć do roli bazy danych modelu za pomocą co najmniej uprawnienia do odczytu. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Łączenie programu Excel

Łączenie z serwerem w programie Excel jest obsługiwana przez użycie funkcji pobierania danych w programie Excel 2016 lub nowszy. Łączenie się przy użyciu Kreatora importu tabeli w dodatku Power Pivot nie jest obsługiwane. 

1. W programie Excel na **danych** wstążki, kliknij przycisk **Pobierz dane zewnętrzne** > **z innych źródeł** > **z usług Analysis Services**.

2. W oknie Kreatora połączenia danych w **nazwy serwera**, wprowadź nazwę serwera, łącznie z protokołem i identyfikatora URI. Na przykład asazure://westcentralus.asazure.windows.net/advworks. Następnie w **poświadczeń logowania**, wybierz opcję **użyj następującej nazwy użytkownika i hasło**, a następnie wpisz nazwę użytkownika w organizacji, na przykład nancy@adventureworks.comi hasło.

    > [!IMPORTANT]
    > Jeśli zalogujesz się Account Microsoft Live ID, Yahoo, Gmail, itp. lub musisz zalogować się przy użyciu usługi Multi-Factor authentication, pozostaw puste pole hasła. Zostanie wyświetlony monit o podanie hasła po kliknięciu przycisku Dalej. 

    ![Łączenie z logowania programu Excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. W **wybierz bazę danych i tabelę**wybierz bazę danych i modelu lub perspektywie, a następnie kliknij przycisk **Zakończ**.
   
    ![Łączenie z programu Excel wybierz model](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Zobacz także

[Biblioteki klienckie](analysis-services-data-providers.md)   
[Zarządzanie serwerem](analysis-services-manage.md)     


