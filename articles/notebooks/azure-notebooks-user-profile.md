---
title: Profil użytkownika i identyfikator dla korzystanie z notesów usługi Azure
description: Jak utworzyć i zarządzać swoimi profilu użytkownika i identyfikator użytkownika przy użyciu notesów usługi Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 7d069d86-660f-4c94-b6e3-0c0f38c52d0e
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: b8c21b908ca9162a7e44c7af1e222babc6ee1eb7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60631983"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>Identyfikator profilu i użytkownika dla notesów usługi Azure

W obszarze Zaawansowane, współpracy notesów usługi Azure profilu użytkownika przedstawia publiczny obraz do innych osób:

[![Na stronie profilu notesy platformy Azure](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Identyfikator użytkownika jest częścią adresów URL umożliwia udostępnianie projektów i notesy. Poniższa lista zawiera opis różnych wzorców adresów URL:

- `https://notebooks.azure.com/<user_id>`: Strony swojego profilu.
- `https://notebooks.azure.com/<user_id>/projects`: Twoich projektów. Zobacz wszystkie projekty; inni użytkownicy widzą tylko projekty publiczne.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Pliki projektu.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Klony określonych projektów.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: Podgląd HTML określonego notesu lub pliku.

## <a name="your-user-id"></a>Identyfikator użytkownika

Podczas logowania się do notesów usługi Azure po raz pierwszy, Twoje konto jest automatycznie przypisywany Identyfikatora tymczasowe użytkownika, takie jak "anon-idr3ca". Tak długo, jak długo ma identyfikator użytkownika, który rozpoczyna się od "anon-", notesy platformy Azure monituje o go zmienić po każdym zalogowaniu:

![Monit o utworzenie Identyfikatora użytkownika podczas logowania się do notesów usługi Azure](media/accounts/create-user-id.png)

A **skonfigurować identyfikator użytkownika** polecenie również pojawia się obok nazwy tymczasowych użytkowników:

![Konfigurowanie polecenia identyfikator użytkownika, który jest wyświetlany, gdy używasz tymczasowy identyfikator](media/accounts/configure-user-id-command.png)

Można również zmienić swój identyfikator użytkownika w dowolnym momencie na stronie profilu.

Nazwa użytkownika musi składać się z między cztery a szesnastu litery, cyfry i łączniki. Inne znaki są niedozwolone, a identyfikator użytkownika nie można rozpocząć lub kończyć znakiem łącznika ani używać wiele łączników w wierszu. Ponieważ nazwy użytkowników są unikatowe w obrębie wszystkich kont notesy platformy Azure, może zostać wyświetlony komunikat, "Identyfikator użytkownika jest już w użyciu." (Komunikat pojawia się również w przypadku próby użycia znakiem towarowym firmy Microsoft jako identyfikator użytkownika.) W takich przypadkach należy wybrać nazwę innego użytkownika.

> [!Important]
> Zmiana Identyfikatora unieważnia wszelkie adresy URL mogą zostały udostępnione przy użyciu swojego identyfikatora poprzedniego. Możesz zmienić swój identyfikator powrót do poprzedniego Identyfikatora w celu ponownego zweryfikowania łącza. Jednak jest możliwe dla innego użytkownika skorzystać z nieużywane Identyfikatora, w tym samym czasie.

## <a name="your-profile"></a>Twój profil

Twój profil składa się z dostępne publicznie informacji pod adresem URL, `https://notebooks.azure.com/<user_id>`. Strony swojego profilu pokazuje również niedawno używanych projektów i wszelkie projekty gwiazdkami.

Aby edytować swój profil, należy użyć **edytować informacje o profilu** polecenia na stronie profilu. Części Twojego profilu są następujące:

| Sekcja | Zawartość |
| --- | --- |
| Zdjęcie profilowe | Obraz, który jest wyświetlany na stronie profilu. |
| Informacje o koncie | Nazwa wyświetlana, identyfikator użytkownika i konto e-mail publicznych. Na konto poczty e-mail zawiera mean skontaktowania się z innym użytkownikom i może różnić się od [konta](azure-notebooks-user-account.md) używasz do logowania się do notesów usługi Azure, sam. |
| Informacje o profilu | Twoja lokalizacja, firmy, stanowisko, witryny sieci web i krótki opis samodzielnie. |
| Profile społecznościowych | GItHub, Twitter i Facebook identyfikatory, jeśli chcesz udostępnić je. |
| Ustawienia prywatności | Udostępnia dwa polecenia:<ul><li>**Eksportuj Mój profil**: powoduje utworzenie i pobranie *zip* plik zawierający wszystkie informacje, które notesy platformy Azure są zapisywane w profilu, w tym Twoje zdjęcie, informacje o profilu i dzienniki zabezpieczeń.</li><li>**Usunąć swoje konto**: Trwale usuwa wszystkie informacje osobiste przechowywane w notesach platformy Azure.</li></ul> |
| Włącz funkcje lokacji | Umożliwia kontrolowanie aspektów zachowania notesy platformy Azure:<ul><li>**Ujednoliconego frontonu dla notesów**: umożliwia szybsze uruchamianie notesu i lepszego stanu trwałego.</li><li>**Domyślnie uruchamiane w JupyterLab**: Domyślnie notesy platformy Azure udostępnia prosty interfejs użytkownika, który jest odpowiedni dla większości użytkowników. JupyterLab zapewnia bardziej rozbudowane, ale bardziej skomplikowanych interfejs dla doświadczonych użytkowników.</li><li>**Witryny sieci Web VNext**: włącza układ zmodernizowane sieci web, pokazana w tej dokumentacji.</li></ul> |

## <a name="next-steps"></a>Kolejne kroki  

> [!div class="nextstepaction"]
> [Samouczek: tworzenie uruchomienia notesu programu Jupyter w celu regresji liniowej](tutorial-create-run-jupyter-notebook.md)
