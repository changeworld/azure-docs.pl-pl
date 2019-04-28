---
title: Zarządzanie usługami sieci Web
titleSuffix: Azure Machine Learning Studio
description: Zarządzaj usługi Machine Learning nowe oraz klasyczne usługi sieci Web przy użyciu portalu usług sieci Web Microsoft Azure Machine Learning. Ponieważ klasycznych usług sieci Web i usług sieci Web nowej opierają się na różnych podstawowej technologii, masz możliwości zarządzania nieco inny dla każdego z nich.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: 711cb674cb00a880eadda11b03da87631df90b0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861739"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-web-services-portal"></a>Zarządzanie usługą sieci web przy użyciu portalu usług sieci Web programu Azure Machine Learning Studio
Aby zarządzać swoimi Machine Learning nowych i klasycznych usług sieci Web przy użyciu portalu usług sieci Web Microsoft Azure Machine Learning. Ponieważ klasycznych usług sieci Web i usług sieci Web nowej opierają się na różnych podstawowej technologii, masz możliwości zarządzania nieco inny dla każdego z nich.

W portalu usług sieci Web Machine Learning możesz wykonywać następujące czynności:

* Monitorowanie sposobu korzystania z usługi sieci web.
* Konfigurowanie opisu i aktualizowanie kluczy dla sieci web service (tylko nowy), zaktualizuj swoje magazynu konta klucza (tylko nowy), Włącz rejestrowanie i włączyć lub wyłączyć przykładowych danych.
* Usuń usługę sieci web.
* Tworzenie, delete lub update rozliczenia plany (tylko nowe).
* Dodawanie i usuwanie punktów końcowych (tylko wersja klasyczna)

>[!NOTE]
>Możesz również określić klasycznych usług sieci web w [usługi Machine Learning Studio](https://studio.azureml.net) na **usług sieci Web** kartę.

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Uprawnienia do zarządzania nowego Menedżera zasobów na podstawie usług sieci web

Nowych usług sieci web są wdrażane jako zasoby platformy Azure. W efekcie musi mieć odpowiednie uprawnienia do wdrażania i zarządzania nowych usług sieci web.  Do wdrażania i zarządzania nią nowych usług sieci web musi mieć przypisaną rolę współautorem lub administratorem subskrypcji, w której wdrażana jest usługa sieci web. Jeśli użytkownik Zaproś innego użytkownika do obszaru roboczego usługi machine learning, należy przypisać je do roli administratora lub współautora w subskrypcji, przed wdrożeniem lub zarządzać usługami sieci web. 

Jeśli użytkownik nie ma odpowiednich uprawnień dostępu do zasobów w portalu usług sieci Web Azure Machine Learning, otrzymają następujący błąd podczas próby wdrożenia usługi sieci web:

*Wdrażanie usługi sieci Web nie powiodło się. To konto ma wystarczające uprawnienia dostępu do subskrypcji platformy Azure, która zawiera obszar roboczy. Aby wdrożyć usługę sieci Web na platformie Azure, to samo konto musi zostać zaproszone do obszaru roboczego i mieć dostęp do subskrypcji platformy Azure, która zawiera obszar roboczy.*

Aby uzyskać więcej informacji na temat tworzenia obszaru roboczego, zobacz [tworzenie i udostępnianie obszaru roboczego usługi Azure Machine Learning Studio](create-workspace.md).

Aby uzyskać więcej informacji o ustawianiu uprawnień dostępu, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Zarządzanie usługami sieci Web nowy
Do zarządzania usługami sieci Web nowy:

1. Zaloguj się do [usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) przy użyciu usługi Microsoft Azure portal konta — Użyj konta, na którym jest skojarzony z subskrypcją platformy Azure.
2. W menu, kliknij polecenie **usług sieci Web**.

Spowoduje to wyświetlenie listy wdrożonych usług sieci Web dla Twojej subskrypcji. 

Aby zarządzać usługą sieci Web, kliknij przycisk usług sieci Web. Ze strony usługi sieci Web można wykonywać następujące czynności:

* Kliknij usługę sieci web do zarządzania nim.
* Kliknij pozycję rozliczenia Plan usługi sieci web można go zaktualizować.
* Usuwanie usługi sieci web.
* Skopiuj usługi sieci web i wdrożyć ją w innym regionie.

Po kliknięciu przycisku usługi sieci web zostanie otwarta strona Szybki Start usługi sieci web. Strony Szybki Start usługi sieci web ma dwie opcje menu, które umożliwiają zarządzanie usługą sieci web:

* **Pulpit NAWIGACYJNY** — służy do wyświetlania użycia usługi sieci Web.
* **Konfiguruj** — umożliwia dodanie tekst opisu zaktualizować klucza konta magazynu skojarzone z usługą sieci Web i włącza lub wyłącza przykładowych danych.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorowanie sposobu korzystania z usługi sieci web
Kliknij przycisk **pulpit NAWIGACYJNY** kartę.

Na pulpicie nawigacyjnym możesz wyświetlić ogólne użycie usługi sieci Web w okresie czasu. Można wybrać okres, aby wyświetlić z menu rozwijanego okresu, w prawym górnym rogu wykresy użycia. Pulpit nawigacyjny zawiera następujące informacje:

* **Żądania względem czasu** wykres kroku liczba żądań w wybranym okresie. Może to pomóc ustalić, czy występują skoki użycia.
* **Odpowiedź na żądanie żądań** wyświetla łączną liczbę wywołań odpowiedź na żądanie usługa odebrała za pośrednictwem wybranego przedziału czasu i ile z nich nie powiodła się.
* **Średni czas obliczenia odpowiedź na żądanie** przedstawia Średni czas potrzebny do wykonania odebranych żądań.
* **Partie żądania** wyświetla łączną liczbę żądań wsadowych usługa odebrała za pośrednictwem wybranego przedziału czasu i ile z nich nie powiodła się.
* **Średnie opóźnienie zadania** przedstawia Średni czas potrzebny do wykonania odebranych żądań.
* **Błędy** Wyświetla łączna liczba błędów, które wystąpiły na wywołania usługi sieci web.
* **Koszty usług** Wyświetla opłaty za plan rozliczeniowy związane z usługą.

### <a name="configuring-the-web-service"></a>Konfigurowanie usługi sieci web
Kliknij przycisk **Konfiguruj** opcji menu.

Można aktualizować następujące właściwości:

* **Opis** umożliwia wprowadzenie opisu usługi sieci Web.
* **Tytuł** umożliwia wprowadź tytuł dla usługi sieci Web
* **Klucze** pozwala wymienić podstawowe i pomocnicze klucze interfejsu API.
* **Klucz konta magazynu** umożliwia zaktualizowanie klucza konta magazynu skojarzonego ze zmianami w usłudze sieci Web. 
* **Włącz przykładowe dane** pozwala na dostarczenie przykładowe dane, które można użyć, aby przetestować usługę odpowiedzi na żądanie. Jeśli utworzono usługę sieci web w usłudze Machine Learning Studio przykładowych danych jest pobierana z danych usługi, które są używane do uczenia modelu. Jeśli utworzono programowo usługę, dane treningowe pochodzą z przykładowych danych, podana jako część pakietu JSON.

### <a name="managing-billing-plans"></a>Zarządzanie plany rozliczeniowe
Kliknij przycisk **plany** opcji menu ze strony szybkiego startu usługi sieci web. Możesz również kliknąć planu skojarzonego z określonej usługi sieci Web do zarządzania tego planu.

* **Nowe** służy do tworzenia nowego planu.
* **Wystąpienie planu dodawania/usuwania** umożliwia "skalowanie" istniejący plan na dodanie pojemności.
* **Uaktualnianie i obniżanie wersji** umożliwia "skalowanie" istniejący plan na dodanie pojemności.
* **Usuń** można usunąć planu.

Kliknij plan, aby wyświetlić jego pulpit nawigacyjny. Pulpit nawigacyjny umożliwia użycie migawki lub planu w wybranym okresie. Aby wybrać okres czasu, aby wyświetlić, kliknij przycisk **okres** listy rozwijanej w prawym górnym rogu pulpitu nawigacyjnego. 

Pulpit nawigacyjny plan zawiera następujące informacje:

* **Opis planu** Wyświetla informacje dotyczące kosztów i wydajności związanych z tym programem.
* **Planowanie użycia** przedstawia liczbę transakcji i godzin obliczeniowych, które zostały rozliczania planu.
* **Usługi sieci Web** Wyświetla liczbę usług sieci Web, które korzystają z tego planu.
* **Najważniejsze wywołania przez usługi sieci Web** Wyświetla wykonywania wywołań, które są powiązane z planu usługi sieci Web pierwsze cztery.
* **Pierwszych usług sieci Web według godzin obliczeniowych** Wyświetla pierwsze cztery usług sieci Web, które korzystają z zasobów obliczeniowych, które są naliczane względem planu.

## <a name="manage-classic-web-services"></a>Zarządzanie klasycznych usług sieci Web
> [!NOTE]
> Procedury przedstawione w tej sekcji mają zastosowanie do zarządzania klasycznych usług sieci web za pośrednictwem portalu usług sieci Web Azure Machine Learning. Aby uzyskać informacji o zarządzaniu klasycznych usług sieci Web za pomocą usługi Machine Learning Studio i witryny Azure portal, zobacz [Zarządzanie obszarem roboczym usługi Azure Machine Learning Studio](manage-workspace.md).
> 
> 

Do zarządzania usługami sieci Web:

1. Zaloguj się do [usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) przy użyciu usługi Microsoft Azure portal konta — Użyj konta, na którym jest skojarzony z subskrypcją platformy Azure.
2. W menu, kliknij polecenie **klasycznych usług sieci Web**.

Aby zarządzać z klasycznej usługi sieci Web, kliknij **klasycznych usług sieci Web**. Na stronie klasycznych usług sieci Web można wykonywać następujące czynności:

* Kliknij usługę sieci web, aby wyświetlić skojarzone punkty końcowe.
* Usuwanie usługi sieci web.

Jeśli zarządzasz klasyczna usługa sieci Web, możesz zarządzać każdego z punktów końcowych oddzielnie. Po kliknięciu przycisku usługi sieci web, na stronie usługi sieci Web zostanie otwarta lista punktów końcowych skojarzonych z usługą. 

Na stronie punktu końcowego klasycznej usługi sieci Web można dodawać i usuwać punkty końcowe usługi. Aby uzyskać więcej informacji dotyczących dodawania punktów końcowych, zobacz [tworzenia punktów końcowych](create-endpoint.md).

Kliknij jeden z punktów końcowych, aby otworzyć stronę Szybki Start usługi sieci web. Na stronie Szybki Start istnieją dwie opcje menu, które umożliwiają zarządzanie usługą sieci web:

* **Pulpit NAWIGACYJNY** — służy do wyświetlania użycia usługi sieci Web.
* **Konfiguruj** — pozwala na dodawanie tekst opisu włączyć rejestrowanie błędów i wyłączyć, zaktualizuj klucz dla konta magazynu skojarzone z usługą sieci Web i włączanie i wyłączanie przykładowych danych.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorowanie sposobu korzystania z usługi sieci web
Kliknij przycisk **pulpit NAWIGACYJNY** kartę.

Na pulpicie nawigacyjnym możesz wyświetlić ogólne użycie usługi sieci Web w okresie czasu. Można wybrać okres, aby wyświetlić z menu rozwijanego okresu, w prawym górnym rogu wykresy użycia. Pulpit nawigacyjny zawiera następujące informacje:

* **Żądania względem czasu** wykres kroku liczba żądań w wybranym okresie. Może to pomóc ustalić, czy występują skoki użycia.
* **Odpowiedź na żądanie żądań** wyświetla łączną liczbę wywołań odpowiedź na żądanie usługa odebrała za pośrednictwem wybranego przedziału czasu i ile z nich nie powiodła się.
* **Średni czas obliczenia odpowiedź na żądanie** przedstawia Średni czas potrzebny do wykonania odebranych żądań.
* **Partie żądania** wyświetla łączną liczbę żądań wsadowych usługa odebrała za pośrednictwem wybranego przedziału czasu i ile z nich nie powiodła się.
* **Średnie opóźnienie zadania** przedstawia Średni czas potrzebny do wykonania odebranych żądań.
* **Błędy** Wyświetla łączna liczba błędów, które wystąpiły na wywołania usługi sieci web.
* **Koszty usług** Wyświetla opłaty za plan rozliczeniowy związane z usługą.

### <a name="configuring-the-web-service"></a>Konfigurowanie usługi sieci web
Kliknij przycisk **Konfiguruj** opcji menu.

Można aktualizować następujące właściwości:

* **Opis** umożliwia wprowadzenie opisu usługi sieci Web. Opis jest polem wymaganym.
* **Rejestrowanie** pozwala włączyć lub wyłączyć rejestrowanie w punkcie końcowym błędów. Aby uzyskać więcej informacji na temat rejestrowania, zobacz Włączanie [rejestrowania usług sieci web Machine Learning](web-services-logging.md).
* **Włącz przykładowe dane** pozwala na dostarczenie przykładowe dane, które można użyć, aby przetestować usługę odpowiedzi na żądanie. Jeśli utworzono usługę sieci web w usłudze Machine Learning Studio przykładowych danych jest pobierana z danych usługi, które są używane do uczenia modelu. Jeśli utworzono programowo usługę, dane treningowe pochodzą z przykładowych danych, podana jako część pakietu JSON.


