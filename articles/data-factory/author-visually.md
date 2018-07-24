---
title: Wizualne Tworzenie usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać wizualnego tworzenia w usłudze Azure Data Factory
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/01/2018
ms.author: shlo
ms.openlocfilehash: 655a6ab2960047cde50bec2953015283ca8577f0
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214861"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Wizualne Tworzenie usługi Azure Data Factory
Usługi Azure Data Factory użytkownika interfejsu środowiska pozwala wizualnie tworzyć i wdrażać zasoby fabryki danych bez konieczności pisania kodu. Można przeciągnąć działania na kanwę potoku, wykonywać przebiegi testowe, interakcyjnie debugować i wdrożyć i monitorować uruchomienia potoków. Dostępne są dwie opcje używania środowiska użytkownika do wykonania wizualnego tworzenia:

- Autor bezpośrednio z usługi Data Factory.
- Tworzenie przy użyciu integrację z usługą Git programu Visual Studio Team Services (VSTS) do współpracy, kontroli źródła lub wersji.

## <a name="author-directly-with-the-data-factory-service"></a>Tworzenie bezpośrednio za pomocą usługi Data Factory
Tworzeniu wizualizacji przy użyciu usługi Data Factory różni się od tworzeniu wizualizacji przy użyciu usługi VSTS na dwa sposoby:

- Usługa Data Factory nie obejmuje repozytorium do przechowywania obiektów JSON dla Twoich zmian.
- Usługa Data Factory nie jest zoptymalizowana pod kątem współpracy lub kontroli wersji.

![Konfigurowanie usługi Data Factory ](media/author-visually/configure-data-factory.png)

Jeśli używasz środowiska użytkownika **Kanwa tworzenia** można tworzyć bezpośrednio w usłudze Data Factory, tylko **Opublikuj wszystkie** tryb jest dostępny. Wszelkie zmiany, które są publikowane bezpośrednio w usłudze Data Factory.

![Tryb publikowania](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>Tworzenie przy użyciu integracji VSTS Git
Tworzeniu wizualizacji przy użyciu integracji VSTS Git obsługuje kontroli źródła i współpracy for work na potoków usługi data factory. Fabryki danych można skojarzyć z repozytorium Git programu VSTS konta na potrzeby kontroli źródła, współpracy, przechowywanie wersji i tak dalej. Na jednym koncie usługi VSTS Git może mieć wiele repozytoriów, ale repozytorium Git usługi VSTS mogą być skojarzone z fabryką danych tylko jeden. Jeśli nie masz konta usługi VSTS lub repozytorium, postępuj zgodnie z [w instrukcjach](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student) do tworzenia zasobów.

> [!NOTE]
> Skrypt i pliki danych można przechowywać w repozytorium GIT usługi VSTS. Jednak trzeba ręcznie przekazać pliki do usługi Azure Storage. Potok usługi Data Factory nie automatycznie przekazać skrypt lub pliki danych przechowywanych w repozytorium GIT usługi VSTS, do usługi Azure Storage.

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>Konfigurowanie repozytorium Git usługi VSTS przy użyciu usługi Azure Data Factory
Repozytorium GIT usługi VSTS można skonfigurować za pomocą usługi data factory, za pomocą dwóch metod.

#### <a name="method1"></a> Metoda konfiguracji 1: strona zaczynajmy

W usłudze Azure Data Factory, przejdź do **zaczynajmy** strony. Wybierz **Konfigurowanie repozytorium kodu**:

![Konfigurowanie repozytorium kodu platformy VSTS](media/author-visually/configure-repo.png)

**Ustawienia repozytorium** zostanie wyświetlone okienko konfiguracji:

![Konfigurowanie ustawień repozytorium kodu](media/author-visually/repo-settings.png)

W okienku wyświetlana następujący kod usługi VSTS ustawień repozytorium:

| Ustawienie | Opis | Wartość |
|:--- |:--- |:--- |
| **Typ repozytorium** | Typ repozytorium kodu platformy VSTS.<br/>**Uwaga**: GitHub nie jest obecnie obsługiwane. | Program Visual Studio Team Services i Git |
| **Azure Active Directory** | Nazwa dzierżawy usługi Azure AD. | <your tenant name> |
| **Konto programu Visual Studio Team Services** | Nazwa konta usługi VSTS. Możesz znaleźć nazwę swojego konta usługi VSTS w `https://{account name}.visualstudio.com`. Możesz [Zaloguj się do konta usługi VSTS](https://www.visualstudio.com/team-services/git/) dostęp do Twojego profilu programu Visual Studio i zobacz projektów i repozytoriów. | <your account name> |
| **ProjectName** | Nazwa projektu usługi VSTS. Możesz znaleźć, jeśli nazwa projektu usługi VSTS w `https://{account name}.visualstudio.com/{project name}`. | <your VSTS project name> |
| **RepositoryName** | Nazwa repozytorium usługi VSTS kodu. Projekty usługi VSTS zawierają repozytoriów Git do zarządzania kodem źródłowym, wraz ze wzrostem natężenia projektu. Możesz utworzyć nowe repozytorium, lub użyć istniejącego repozytorium, który jest już w projekcie. | <your VSTS code repository name> |
| **Gałąź współpracy** | Gałąź współpracy usługi VSTS, który będzie używany do publikowania. Domyślnie jest `master`. To zmienić w przypadku, gdy chcesz opublikować zasobów z innej gałęzi. | <your collaboration branch name> |
| **Folder główny** | Folderem w gałęzi współpracy usługi VSTS. | <your root folder name> |
| **Importuj istniejące zasoby fabryki danych do repozytorium** | Określa, czy Importuj istniejące zasoby fabryki danych ze środowiska użytkownika **Kanwa tworzenia** do repozytorium Git usługi VSTS. Zaznacz pole, aby zaimportować swoje zasoby fabryki danych do skojarzonego repozytorium Git w formacie JSON. Ta akcja Eksportuje każdy zasób osobno (czyli usługi połączone i zestawy danych są eksportowane do oddzielnych JSON Smb1sessionsetup). Gdy to pole nie jest zaznaczone, istniejące zasoby nie są importowane. | Wybrane (ustawienie domyślne) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metoda konfiguracji 2: tworzenie obszaru roboczego środowiska użytkownika
W Interfejsie usługi Azure Data Factory **Kanwa tworzenia**, zlokalizuj fabryką danych. Wybierz **usługi Data Factory** menu rozwijanego, a następnie wybierz **Konfiguruj repozytorium kodu**.

Zostanie wyświetlone okienko konfiguracji. Aby uzyskać szczegółowe informacje o ustawieniach konfiguracji, zobacz opisy w <a href="#method1">metody konfiguracji 1</a>.

![Konfigurowanie ustawień repozytorium kodu na potrzeby tworzenia interfejsu użytkownika](media/author-visually/configure-repo-2.png)

#### <a name="switch-to-a-different-git-repo"></a>Przełącz się do innego repozytorium Git

Aby przełączyć się do innego repozytorium Git, Znajdź ikonę w prawym górnym rogu strony Przegląd usługi Data Factory, jak pokazano na poniższym zrzucie ekranu. Jeśli nie widzisz ikonę wyczyścić pamięci podręcznej lokalnej przeglądarki. Wybierz ikonę, aby usunąć skojarzenie z bieżącego repozytorium.

Po usunięciu skojarzenia z bieżącego repozytorium, można skonfigurować ustawienia Git, aby użyć innego repozytorium. Następnie można zaimportować istniejące zasoby fabryki danych do nowego repozytorium.

![Usuń skojarzenie z bieżącego repozytorium Git.](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>Korzystanie z kontroli wersji
Systemy kontroli wersji (znany także jako _kontroli źródła_) umożliwiają deweloperom współpracować nad kodu i śledzenie zmian wprowadzonych do kodu podstawowego. Kontrola źródła jest niezbędnego narzędzia dla deweloperów wielu projektów.

Każde repozytorium Git usługi VSTS, która jest skojarzona z fabryką danych ma gałąź pracy zespołowej. (`master` to gałąź domyślna współpracy). Użytkownicy mogą również tworzyć gałęzie funkcji, klikając **+ nowa gałąź** i Programowanie w gałęzie funkcji.

![Zmień kod, synchronizowanie i publikowania](media/author-visually/sync-publish.png)

Gdy jesteś gotowy z programowaniem funkcji w gałęzi funkcji, możesz kliknąć **Utwórz żądanie ściągnięcia**. Spowoduje to przejście do usługi GIT usługi VSTS, gdzie można podnieść ściągnięcia żądania, przeglądami kodu i scalania zmian w gałęzi współpracy. (`master` jest ustawieniem domyślnym). Tylko możesz opublikować w usłudze Data Factory ze swojej gałęzi współpracy. 

![Utwórz nowe żądanie ściągnięcia](media/author-visually/create-pull-request.png)

#### <a name="publish-code-changes"></a>Publikowanie zmian w kodzie
Po zostały scalone zmiany w gałęzi współpracy (`master` jest ustawieniem domyślnym), wybierz opcję **Publikuj** ręcznie opublikować zmiany kodu w gałęzi głównej w usłudze Data Factory.

![Publikowanie zmian w usłudze Data Factory](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> Gałąź główna nie jest językiem co to jest wdrożony w usłudze Data Factory. Gałąź główna *musi* można ręcznie opublikować w usłudze Data Factory.

## <a name="use-the-expression-language"></a>Użyj języka wyrażeń
Wyrażenia wartości właściwości można określić przy użyciu języka wyrażeń, który jest obsługiwany przez usługę Azure Data Factory. 

Określ wyrażeń dla wartości właściwości, wybierając **Dodawanie zawartości dynamicznej**:

![Użyj języka wyrażeń](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>Użyj funkcji i parametrów

Możesz użyć funkcji lub określ parametry w celu potoków i zestawów danych w usłudze Data Factory **Konstruktor wyrażeń**:

Aby uzyskać informacji na temat obsługiwanych wyrażeń, zobacz [wyrażeń i funkcji w usłudze Azure Data Factory](control-flow-expression-language-functions.md).

![Dodaj zawartość dynamiczną](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>Przekazywanie opinii
Wybierz **opinii** komentarz na temat funkcji lub powiadomić firmę Microsoft o problemach, za pomocą narzędzia:

![Opinia](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat monitorowania i zarządzania potoków, zobacz [monitorowanie potoków i zarządzanie nimi programistycznie](monitor-programmatically.md).
