---
title: Najlepsze rozwiązania platformy Azure Stack weryfikacji | Dokumentacja firmy Microsoft
description: W tym artykule opisano najlepsze rozwiązania dotyczące używania weryfikacji jako usługa.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/15/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: cc2d19bec91998116143058d2bc4313fd192db38
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56592977"
---
# <a name="create-an-oem-package"></a>Tworzenie pakietu producenta OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Pakiet rozszerzenia usługi Azure Stack OEM to mechanizm, który OEM określonej zawartości zostanie dodany do infrastruktury Azure Stack, do użytku w wdrożenia, jak i procesów operacyjnych, takich jak aktualizacja, rozszerzenia i zastąpienie pola.

## <a name="creating-the-package"></a>Tworzenie pakietu

Po utworzeniu i zweryfikowany, pakiet rozszerzenia OEM może służyć w VaaS.  Przed kontynuowaniem upewnij się, że zostały wykonane kroki [tworzenia pakietu OEM](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true). Pakiet jest następnie przesłane do firmy Microsoft oraz VaaS wyników testów z przepływu pracy weryfikacji pakietu rejestracji. Poniższe kroki zawierają szczegółowe instrukcje dotyczące pakietu wygenerowane pliki w pliku zip pojedynczego, którą może wykorzystać VaaS.

1. Zidentyfikuj następującej zawartości pakietu:
    - Plik zip zawierający zawartość pakietu
    - Plik manifestu o nazwie `oemMetadata.xml`, powinna być identyczna zawartość pliku metadata.xml w katalogu głównym zawartości pakietu.

2. Wybierz pliki zawartości, a następnie utwórz plik zip z zawartości:

    ![Zawartość pliku zip](media/vaas-create-oem-package-1.png) ![Kompresuj zawartość elementu](media/vaas-create-oem-package-2.png)

3. Zmień nazwę wynikowy plik, więc opisowy, umożliwiające identyfikację.

## <a name="verifying-the-contents"></a>Weryfikowanie zawartości

Aby sprawdzić poprawność struktury pliku zip, sprawdź go i sprawdź, czy są bez podfolderów. Najwyższego poziomu ma zawartość plików z rozszerzeniem zip. Poniżej przedstawiono strukturę prawidłowy pakiet.
> [!IMPORTANT]
> Kompresowanie do folderu nadrzędnego, zamiast zawartości spowoduje, że pakiet podpisywania nie powiedzie się.

![Poprawnie pakietu zawartości](media/vaas-create-oem-package-3.png)

Można teraz przekazywane do VaaS pliku zip i podpisany przez firmę Microsoft w przepływie pracy sprawdzanie poprawności pakietu.

## <a name="next-steps"></a>Kolejne kroki

- [Sprawdzanie poprawności pakietu producenta OEM](azure-stack-vaas-validate-oem-package.md)
