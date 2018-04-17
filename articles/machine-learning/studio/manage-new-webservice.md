---
title: Za pomocą portalu usługi sieci Web systemu Azure Machine Learning | Dokumentacja firmy Microsoft
description: Zarządzanie dostępem do usługi Azure Machine Learning obszary robocze oraz wdrażania i zarządzania nimi usług sieci web interfejsu API uczenia Maszynowego
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: b62cf2ca-dd2a-4a83-bb54-469f948fb026
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.openlocfilehash: c02ded6605b6374e0e37462205f8390f54b24b58
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>Zarządzanie usługą sieci Web przy użyciu portalu usługi sieci Web systemu Azure Machine Learning
Można Zarządzanie Machine Learning nowy i usług sieci Web klasycznego przy użyciu portalu usługi sieci Web Microsoft Azure Machine Learning. Ponieważ usługi nowej sieci Web i usług sieci Web klasycznego są oparte na różnych technologii podstawowej, masz możliwości zarządzania nieco inne dla każdego z nich.

W portalu usługi sieci Web usługi Machine Learning, które można wykonywać następujące czynności:

* Monitorowanie, sposobu używania usługi sieci web.
* Skonfiguruj opis, zaktualizuj klucze dla sieci web usługi (tylko nowy), aktualizacja Twojego magazynu konta klucza (tylko nowy), Włącz rejestrowanie i włączyć lub wyłączyć przykładowych danych.
* Usuń usługę sieci web.
* Tworzenie, delete lub update rozliczeń planów (tylko nowy).
* Dodawanie i usuwanie punktów końcowych (tylko klasyczne)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Usługi sieci web na podstawie uprawnień do zarządzania nowego Menedżera zasobów

Nowe usługi sieci web są wdrażane jako zasobów platformy Azure. W efekcie musi mieć odpowiednie uprawnienia do wdrażania i zarządzania nowych usług sieci web.  Aby wdrożyć lub zarządzać nowych usług sieci web musi mieć przypisaną rolę współautora lub administrator w subskrypcji, w której wdrażana jest usługa sieci web. Jeśli musisz poprosić innego użytkownika do obszaru roboczego uczenia maszynowego, należy je przypisać do roli współautora lub administratora dla subskrypcji przed wdrożeniem lub zarządzania usługami sieci web. 

Jeśli użytkownik ma odpowiednie uprawnienia dostępu do zasobów w portalu usługi sieci Web systemu Azure Machine Learning, ich zostanie wyświetlony następujący błąd podczas próby wdrożenia usługi sieci web:

*Wdrażanie usługi sieci Web nie powiodło się. To konto ma wystarczające prawa dostępu do subskrypcji platformy Azure, która zawiera obszar roboczy. Aby wdrożyć usługę sieci Web na platformie Azure, to samo konto musi być zapraszani do obszaru roboczego i uzyskać dostęp do subskrypcji platformy Azure, która zawiera obszar roboczy.*

Aby uzyskać więcej informacji na temat tworzenia obszaru roboczego, zobacz [tworzenie i udostępnianie obszaru roboczego uczenia maszynowego Azure](create-workspace.md).

Aby uzyskać więcej informacji o ustawianiu uprawnień dostępu, zobacz [Wyświetl przypisania dostępu dla użytkowników i grup w portalu Azure - publicznej wersji zapoznawczej](../../role-based-access-control/role-assignments-users.md).


## <a name="manage-new-web-services"></a>Zarządzanie usługami nowej sieci Web
Do zarządzania usługami nowej sieci Web:

1. Zaloguj się do [usługi sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portalu za pomocą platformy Microsoft Azure konta — Użyj konta, na którym jest skojarzony z subskrypcją platformy Azure.
2. W menu, kliknij polecenie **usług sieci Web**.

Spowoduje to wyświetlenie listy wdrożonych usług sieci Web dla Twojej subskrypcji. 

Aby zarządzać usługą sieci Web, kliknij opcję usługi sieci Web. Na stronie usługi sieci Web można:

* Kliknij usługę sieci web do zarządzania nim.
* Kliknij przycisk rozliczeń Plan usługi sieci web do jego aktualizacji.
* Usuwanie usługi sieci web.
* Skopiuj usługi sieci web i wdrożyć ją w innym regionie.

Po kliknięciu usługi sieci web zostanie otwarta strona Szybki Start usługi sieci web. Na stronie szybkiego startu usługi sieci web ma dwie opcje menu, które umożliwiają zarządzanie usługi sieci web:

* **Pulpit NAWIGACYJNY** — służy do wyświetlania użycia usługi sieci Web.
* **Konfiguruj** — umożliwia dodanie tekst opisowy zaktualizować klucza dla konta magazynu skojarzone z usługą sieci Web i włączyć lub wyłączyć przykładowych danych.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorowanie, sposobu używania usługi sieci web
Kliknij przycisk **pulpitu NAWIGACYJNEGO** kartę.

Z poziomu pulpitu nawigacyjnego można wyświetlić ogólne użycie usługi sieci Web w danym okresie czasu. Można wybrać okres, aby wyświetlić z menu rozwijanego okresu, w prawym górnym rogu wykresy użycia. Pulpit nawigacyjny zawiera następujące informacje:

* **Żądań w czasie** wykres krok to liczba żądań przez wybrany okres czasu. Może pomóc ustalić, czy występują wzrostów użycia.
* **Żądanie-odpowiedź żądań** Wyświetla całkowitą liczbę wywołań żądań i odpowiedzi usługa odebrała przez wybrany okres czasu i ile z nich nie powiodło się.
* **Średni czas obliczeniowe żądanie-odpowiedź** wyświetla średni czas potrzebny na wykonanie odebranych żądań.
* **Partie żądania** Wyświetla całkowitą liczbę żądań wsadowych usługa odebrała przez wybrany okres czasu i ile z nich nie powiodło się.
* **Średni czas oczekiwania zadania** wyświetla średni czas potrzebny na wykonanie odebranych żądań.
* **Błędy** Wyświetla łączna liczba błędów, które wystąpiły na połączenia z usługą sieci web.
* **Koszty usługi** Wyświetla opłat za plan rozliczeniowy skojarzony z usługą.

### <a name="configuring-the-web-service"></a>Konfigurowanie usługi sieci web
Kliknij przycisk **Konfiguruj** opcji menu.

Można aktualizować następujące właściwości:

* **Opis elementu** umożliwia wprowadzenie opisu usługi sieci Web.
* **Tytuł** umożliwia wprowadź tytuł dla usługi sieci Web
* **Klucze** umożliwia Obróć klucze interfejsu API podstawowe i pomocnicze.
* **Klucz konta magazynu** umożliwia zaktualizowanie klucza dla konta magazynu skojarzone z zmiany usługi sieci Web. 
* **Włącz przykładowe dane** umożliwia podanie przykładowych danych, które służy do testowania usługi żądań i odpowiedzi. Jeśli utworzono usługę sieci web w usłudze Machine Learning Studio przykładowych danych jest pobierana z danych użytkownika używanych do uczenia modelu. Jeśli usługi zostały utworzone programowo, dane są pobierane z przykładowe dane podane jako część pakietu JSON.

### <a name="managing-billing-plans"></a>Zarządzanie planami rozliczeń
Kliknij przycisk **plany** opcji menu ze strony Szybki Start usługi sieci web. Możesz również kliknąć planu powiązanego z określonej usługi sieci Web do zarządzania tego planu.

* **Nowe** służy do tworzenia nowego planu.
* **Dodaj lub usuń Plan wystąpienia** umożliwia "skalowanie" istniejący plan do zwiększenia pojemności.
* **Uaktualnianie i obniżanie wersji** umożliwia "skalowanie w górę" istniejący plan do zwiększenia pojemności.
* **Usuń** umożliwia usuwanie planu.

Kliknij plan, aby wyświetlić jego pulpitu nawigacyjnego. Pulpit nawigacyjny umożliwia użycie migawki lub plan przez wybrany czas. Aby wybrać okres do wyświetlania, kliknij przycisk **okres** listy rozwijanej w prawym górnym rogu pulpitu nawigacyjnego. 

Pulpit nawigacyjny plan zawiera następujące informacje:

* **Opis planu** zawiera informacje o kosztów i wydajności związanych z tym programem.
* **Planowanie użycia** Wyświetla liczbę transakcji i godziny obliczeń, które zostały prawidłowo przed planu.
* **Usługi sieci Web** Wyświetla liczbę usług sieci Web, które korzystają z tego planu.
* **Wywołania przez usługi sieci Web z góry** Wyświetla usługi sieci Web pierwszych czterech wykonywania wywołań, które są powiązane z planem.
* **Usługi sieci Web z góry za obliczeniowe godz** Wyświetla pierwszych czterech usługi sieci Web korzystających z zasobów obliczeniowych, które są powiązane z planu.

## <a name="manage-classic-web-services"></a>Zarządzanie usługami sieci Web klasycznego
> [!NOTE]
> Procedury przedstawione w tej sekcji mają zastosowanie do zarządzania usługami sieci web klasycznego portalu usługi sieci Web systemu Azure Machine Learning. Aby uzyskać informacji o zarządzaniu klasycznych sieci Web usługi Machine Learning Studio i portalu Azure, zobacz [Zarządzanie obszarem roboczym usługi Azure Machine Learning](manage-workspace.md).
> 
> 

Do zarządzania usługami sieci Web klasycznego:

1. Zaloguj się do [usługi sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portalu za pomocą platformy Microsoft Azure konta — Użyj konta, na którym jest skojarzony z subskrypcją platformy Azure.
2. W menu, kliknij polecenie **klasycznym usługi sieci Web**.

Aby zarządzać klasycznym usługi sieci Web, kliknij przycisk **klasycznym usługi sieci Web**. Na stronie klasycznym usługi sieci Web można:

* Kliknij usługę sieci web, aby wyświetlić skojarzone punkty końcowe.
* Usuwanie usługi sieci web.

Jeśli zarządzasz usługi sieci Web klasycznego zarządzasz każdego z punktów końcowych oddzielnie. Po kliknięciu usługi sieci web na stronie usługi sieci Web otwiera listę punktów końcowych skojarzonych z usługą. 

Na stronie punkt końcowy usługi sieci Web klasycznego można dodawać i usuwać punktów końcowych w usłudze. Aby uzyskać więcej informacji dotyczących dodawania punktów końcowych, zobacz [tworzenie punktów końcowych](create-endpoint.md).

Kliknij jeden z punktów końcowych, aby otworzyć stronę Szybki Start usługi sieci web. Na stronie szybkiego startu istnieją dwie opcje menu, które umożliwiają zarządzanie usługi sieci web:

* **Pulpit NAWIGACYJNY** — służy do wyświetlania użycia usługi sieci Web.
* **Konfiguruj** — umożliwia dodanie tekst opisowy, włączyć rejestrowanie błędów i wyłączyć, zaktualizować klucza dla konta magazynu skojarzone z usługą sieci Web i włączanie i wyłączanie przykładowych danych.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorowanie, sposobu używania usługi sieci web
Kliknij przycisk **pulpitu NAWIGACYJNEGO** kartę.

Z poziomu pulpitu nawigacyjnego można wyświetlić ogólne użycie usługi sieci Web w danym okresie czasu. Można wybrać okres, aby wyświetlić z menu rozwijanego okresu, w prawym górnym rogu wykresy użycia. Pulpit nawigacyjny zawiera następujące informacje:

* **Żądań w czasie** wykres krok to liczba żądań przez wybrany okres czasu. Może pomóc ustalić, czy występują wzrostów użycia.
* **Żądanie-odpowiedź żądań** Wyświetla całkowitą liczbę wywołań żądań i odpowiedzi usługa odebrała przez wybrany okres czasu i ile z nich nie powiodło się.
* **Średni czas obliczeniowe żądanie-odpowiedź** wyświetla średni czas potrzebny na wykonanie odebranych żądań.
* **Partie żądania** Wyświetla całkowitą liczbę żądań wsadowych usługa odebrała przez wybrany okres czasu i ile z nich nie powiodło się.
* **Średni czas oczekiwania zadania** wyświetla średni czas potrzebny na wykonanie odebranych żądań.
* **Błędy** Wyświetla łączna liczba błędów, które wystąpiły na połączenia z usługą sieci web.
* **Koszty usługi** Wyświetla opłat za plan rozliczeniowy skojarzony z usługą.

### <a name="configuring-the-web-service"></a>Konfigurowanie usługi sieci web
Kliknij przycisk **Konfiguruj** opcji menu.

Można aktualizować następujące właściwości:

* **Opis elementu** umożliwia wprowadzenie opisu usługi sieci Web. Opis jest polem wymaganym.
* **Rejestrowanie** umożliwia włączanie lub wyłączanie rejestrowania w punkcie końcowym błędów. Aby uzyskać więcej informacji, zobacz Włącz [rejestrowania dla usług sieci web uczenie maszynowe](web-services-logging.md).
* **Włącz przykładowe dane** umożliwia podanie przykładowych danych, które służy do testowania usługi żądań i odpowiedzi. Jeśli utworzono usługę sieci web w usłudze Machine Learning Studio przykładowych danych jest pobierana z danych użytkownika używanych do uczenia modelu. Jeśli usługi zostały utworzone programowo, dane są pobierane z przykładowe dane podane jako część pakietu JSON.


