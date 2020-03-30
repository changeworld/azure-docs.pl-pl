---
title: Zarządzanie usługami sieci Web
titleSuffix: ML Studio (classic) - Azure
description: Zarządzanie nowymi i klasycznymi usługami sieci Web w usłudze Machine Learning firmy Microsoft Azure za pomocą portalu usług azure machine learning. Ponieważ klasyczne usługi sieci Web i nowe usługi sieci Web są oparte na różnych podstawowych technologiach, masz nieco inne możliwości zarządzania dla każdego z nich.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: 2277aa3de5955efe5a3e4cb938fa557352f89006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217975"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Zarządzanie usługą sieci Web przy użyciu portalu usług Azure Machine Learning Studio (klasyczny) usług sieci Web

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Usługi azure machine learning nowe i klasyczne usługi sieci Web można zarządzać za pomocą portalu usług azure machine learning web services. Ponieważ klasyczne usługi sieci Web i nowe usługi sieci Web są oparte na różnych podstawowych technologiach, masz nieco inne możliwości zarządzania dla każdego z nich.

W portalu usług sieci Web uczenia maszynowego można:

* Monitoruj sposób obsługi usługi sieci web.
* Skonfiguruj opis, zaktualizuj klucze usługi sieci web (tylko nowe), zaktualizuj klucz konta magazynu (tylko nowy), włącz rejestrowanie oraz włącz lub wyłącz przykładowe dane.
* Usuń usługę sieci web.
* Tworzenie, usuwanie lub aktualizowanie planów rozliczeniowych (tylko nowe).
* Dodawanie i usuwanie punktów końcowych (tylko klasyczne)

>[!NOTE]
>Można również zarządzać klasycznymi usługami sieci web w [umiaście Machine Learning Studio (klasycznym)](https://studio.azureml.net) na karcie **Usługi sieci Web.**

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Uprawnienia do zarządzania usługami sieci web opartymi na New Resources Manager

Nowe usługi sieci web są wdrażane jako zasoby platformy Azure. W związku z tym musisz mieć odpowiednie uprawnienia do wdrażania nowych usług sieci web i zarządzania nimi.  Aby wdrożyć nowe usługi sieci web lub zarządzać nimi, należy przypisać rolę współautora lub administratora w subskrypcji, do której wdrożona jest usługa sieci web. Jeśli zaprosisz innego użytkownika do obszaru roboczego uczenia maszynowego, musisz przypisać go do roli współautora lub administratora w ramach subskrypcji, zanim będzie mógł wdrożyć usługi sieci web lub nimi zarządzać. 

Jeśli użytkownik nie ma odpowiednich uprawnień dostępu do zasobów w portalu usług Azure Machine Learning Web Services, podczas próby wdrożenia usługi sieci web zostanie wyświetlony następujący błąd:

*Wdrożenie usługi sieci Web nie powiodło się. To konto nie ma wystarczającego dostępu do subskrypcji platformy Azure, która zawiera obszar roboczy. Aby wdrożyć usługę sieci Web na platformie Azure, to samo konto musi zostać zaproszone do obszaru roboczego i mieć dostęp do subskrypcji platformy Azure, która zawiera obszar roboczy.*

Aby uzyskać więcej informacji na temat tworzenia obszaru roboczego, zobacz [Tworzenie i udostępnianie obszaru roboczego usługi Azure Machine Learning Studio (klasycznego).](create-workspace.md)

Aby uzyskać więcej informacji na temat ustawiania uprawnień dostępu, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC i portalu Azure](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Zarządzanie nowymi usługami sieci Web
Aby zarządzać nowymi usługami sieci Web:

1. Zaloguj się do portalu [usług azure machine learning web](https://services.azureml.net/quickstart) przy użyciu konta Microsoft Azure — użyj konta skojarzonego z subskrypcją platformy Azure.
2. W menu kliknij polecenie **Usługi sieci Web**.

Spowoduje to wyświetlenie listy wdrożonych usług sieci Web dla subskrypcji. 

Aby zarządzać usługą sieci Web, kliknij pozycję Usługi sieci Web. Na stronie Usługi sieci Web można:

* Kliknij usługę sieci web, aby nią zarządzać.
* Kliknij plan rozliczeniowy dla usługi sieci web, aby ją zaktualizować.
* Usuwanie usługi sieci web.
* Skopiuj usługę sieci web i wdrożyć ją w innym regionie.

Po kliknięciu usługi sieci web zostanie otwarta strona Szybkiego startu usługi sieci web. Strona Szybkiego startu usługi sieci web ma dwie opcje menu, które umożliwiają zarządzanie usługą sieci Web:

* **DASHBOARD** — umożliwia wyświetlanie użycia usługi sieci Web.
* **KONFIGURUJ** — umożliwia dodawanie tekstu opisowego, aktualizowanie klucza dla konta magazynu skojarzonego z usługą sieci Web oraz włączanie lub wyłączanie przykładowych danych.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorowanie sposobu wykorzystania usługi sieci web
Kliknij kartę **DASHBOARD.**

Na pulpicie nawigacyjnym można wyświetlać ogólne użycie usługi sieci Web przez pewien czas. Kropkę można wybrać z menu rozwijanego Okres w prawym górnym rogu wykresów użycia. Na pulpicie nawigacyjnym są wyświetlane następujące informacje:

* **Żądania w czasie** wyświetla wykres krokowy liczby żądań w wybranym okresie czasu. Może pomóc w określeniu, jeśli występują skoki użycia.
* **Żądania odpowiedzi na żądanie** wyświetla całkowitą liczbę wywołań żądania odpowiedzi, które usługa otrzymała w wybranym okresie czasu i ile z nich nie powiodło się.
* **Średni czas obliczania odpowiedzi na żądanie** wyświetla średnią czas potrzebny do wykonania odebranych żądań.
* **Żądania wsadowe** wyświetla całkowitą liczbę żądań partii odebranych przez usługę w wybranym okresie czasu i liczbę z nich nie powiodło się.
* **Średnie opóźnienie zadania** wyświetla średnią czasu potrzebnego do wykonania odebranych żądań.
* **Błędy** wyświetla zagregowaną liczbę błędów, które wystąpiły w wywołaniach do usługi sieci web.
* **Koszty usług** wyświetla opłaty za plan rozliczeniowy skojarzony z usługą.

### <a name="configuring-the-web-service"></a>Konfigurowanie usługi sieci web
Kliknij opcję menu **KONFIGURUJ.**

Można zaktualizować następujące właściwości:

* **Opis** umożliwia wprowadzenie opisu usługi sieci Web.
* **Tytuł** umożliwia wprowadzenie tytułu usługi sieci Web
* **Klawisze** umożliwiają obracanie podstawowych i pomocniczych kluczy interfejsu API.
* **Klucz konta magazynu** umożliwia zaktualizowanie klucza dla konta magazynu skojarzonego ze zmianami usługi sieci Web. 
* **Włącz przykładowe dane** umożliwia podanie przykładowych danych, których można użyć do przetestowania usługi Żądanie-Odpowiedź. Jeśli utworzono usługę sieci web w usłudze Machine Learning Studio (klasyczna), przykładowe dane są pobierane z danych używanych do uczenia modelu. Jeśli usługa została utworzona programowo, dane są pobierane z przykładowych danych podanych jako część pakietu JSON.

### <a name="managing-billing-plans"></a>Zarządzanie planami rozliczeń
Kliknij opcję menu **Plany** na stronie Szybki start usług internetowych. Można również kliknąć plan skojarzony z określoną usługą sieci Web, aby zarządzać tym planem.

* **Nowy** umożliwia utworzenie nowego planu.
* **Dodaj/Usuń plan wystąpienie** pozwala na "skalowanie w poziomie" istniejącego planu, aby dodać pojemność.
* **Upgrade/DownGrade** pozwala na "skalowanie w górę" istniejącego planu, aby dodać pojemność.
* **Polecenie Usuń** umożliwia usunięcie planu.

Kliknij plan, aby wyświetlić jego pulpit nawigacyjny. Pulpit nawigacyjny zapewnia migawkę lub planowanie użycia w wybranym okresie czasu. Aby wybrać okres do wyświetlenia, kliknij pozycję Rozwijana **Okres** w prawym górnym rogu pulpitu nawigacyjnego. 

Pulpit nawigacyjny planu zawiera następujące informacje:

* **Opis planu** wyświetla informacje o kosztach i zdolnościach produkcyjnych skojarzonych z planem.
* **Użycie planu** wyświetla liczbę transakcji i godzin obliczeń, które zostały obciążone planem.
* **Usługi sieci Web** wyświetla liczbę usług sieci Web, które korzystają z tego planu.
* **Top Web Service By Calls** wyświetla cztery najlepsze usługi sieci Web, które nawiązują połączenia, które są naliczane od planu.
* **Top Web Services by Compute Hrs** wyświetla cztery najlepsze usługi sieci Web, które korzystają z zasobów obliczeniowych, które są naliczane od planu.

## <a name="manage-classic-web-services"></a>Zarządzanie klasycznymi usługami sieci Web
> [!NOTE]
> Procedury w tej sekcji są istotne dla zarządzania klasycznymi usługami sieci web za pośrednictwem portalu usług Azure Machine Learning Web Services. Aby uzyskać informacje na temat zarządzania klasycznymi usługami sieci Web za pośrednictwem studia Machine Learning Studio (klasycznego) i portalu Azure, zobacz [Zarządzanie klasycznym obszarem roboczym usługi Azure Machine Learning Studio (klasycznym).](manage-workspace.md)
> 
> 

Aby zarządzać klasycznymi usługami sieci Web:

1. Zaloguj się do portalu [usług azure machine learning web](https://services.azureml.net/quickstart) przy użyciu konta Microsoft Azure — użyj konta skojarzonego z subskrypcją platformy Azure.
2. W menu kliknij polecenie **Klasyczne usługi sieci Web**.

Aby zarządzać klasyczną usługą sieci Web, kliknij **klasyczne usługi sieci Web**. Na stronie Klasyczne usługi sieci Web można:

* Kliknij usługę sieci web, aby wyświetlić skojarzone punkty końcowe.
* Usuwanie usługi sieci web.

Podczas zarządzania klasyczną usługą sieci Web zarządzasz każdym punktem końcowym oddzielnie. Po kliknięciu usługi sieci web na stronie Usługi sieci Web zostanie otwarta lista punktów końcowych skojarzonych z usługą. 

Na stronie klasycznego punktu końcowego usługi sieci Web można dodawać i usuwać punkty końcowe usługi. Aby uzyskać więcej informacji na temat dodawania punktów końcowych, zobacz [Tworzenie punktów końcowych](create-endpoint.md).

Kliknij jeden z punktów końcowych, aby otworzyć stronę Szybki start usługi sieci web. Na stronie Szybki start dostępne są dwie opcje menu, które umożliwiają zarządzanie usługą sieci Web:

* **DASHBOARD** — umożliwia wyświetlanie użycia usługi sieci Web.
* **KONFIGURUJ** — umożliwia dodawanie tekstu opisowego, włączanie i wyłączanie rejestrowania błędów, aktualizowanie klucza konta magazynu skojarzonego z usługą sieci Web oraz włączanie i wyłączanie przykładowych danych.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorowanie sposobu wykorzystania usługi sieci web
Kliknij kartę **DASHBOARD.**

Na pulpicie nawigacyjnym można wyświetlać ogólne użycie usługi sieci Web przez pewien czas. Kropkę można wybrać z menu rozwijanego Okres w prawym górnym rogu wykresów użycia. Na pulpicie nawigacyjnym są wyświetlane następujące informacje:

* **Żądania w czasie** wyświetla wykres krokowy liczby żądań w wybranym okresie czasu. Może pomóc w określeniu, jeśli występują skoki użycia.
* **Żądania odpowiedzi na żądanie** wyświetla całkowitą liczbę wywołań żądania odpowiedzi, które usługa otrzymała w wybranym okresie czasu i ile z nich nie powiodło się.
* **Średni czas obliczania odpowiedzi na żądanie** wyświetla średnią czas potrzebny do wykonania odebranych żądań.
* **Żądania wsadowe** wyświetla całkowitą liczbę żądań partii odebranych przez usługę w wybranym okresie czasu i liczbę z nich nie powiodło się.
* **Średnie opóźnienie zadania** wyświetla średnią czasu potrzebnego do wykonania odebranych żądań.
* **Błędy** wyświetla zagregowaną liczbę błędów, które wystąpiły w wywołaniach do usługi sieci web.
* **Koszty usług** wyświetla opłaty za plan rozliczeniowy skojarzony z usługą.

### <a name="configuring-the-web-service"></a>Konfigurowanie usługi sieci web
Kliknij opcję menu **KONFIGURUJ.**

Można zaktualizować następujące właściwości:

* **Opis** umożliwia wprowadzenie opisu usługi sieci Web. Opis jest polem wymaganym.
* **Rejestrowanie** umożliwia włączenie lub wyłączenie rejestrowania błędów w punkcie końcowym. Aby uzyskać więcej informacji na temat rejestrowania, zobacz [Włączanie rejestrowania dla usług sieci Web usługi uczenia maszynowego](web-services-logging.md).
* **Włącz przykładowe dane** umożliwia podanie przykładowych danych, których można użyć do przetestowania usługi Żądanie-Odpowiedź. Jeśli utworzono usługę sieci web w usłudze Machine Learning Studio (klasyczna), przykładowe dane są pobierane z danych używanych do uczenia modelu. Jeśli usługa została utworzona programowo, dane są pobierane z przykładowych danych podanych jako część pakietu JSON.


