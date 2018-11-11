---
ms.assetid: ''
title: Usługa Azure Key Vault usuwania nietrwałego | Dokumentacja firmy Microsoft
ms.service: key-vault
ms.topic: conceptual
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 09/25/2017
ms.openlocfilehash: 12b14b87a02619b21e80436c80a284c4011f8b33
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300323"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Omówienie usuwania nietrwałego w usłudze Azure Key Vault

Funkcja usuwania nietrwałego w usłudze Key Vault umożliwia odzyskiwanie usuniętych magazynów i magazynu obiektów, określane jako opcji soft-delete. W szczególności firma Microsoft zajęcia się następującymi scenariuszami:

- Obsługa do usunięcia możliwych do odzyskania magazynu kluczy
- Obsługa do usunięcia możliwych do odzyskania obiektów magazynu kluczy (np.) klucze, wpisy tajne certyfikatów)

## <a name="supporting-interfaces"></a>Obsługa interfejsów

Funkcja usuwania nietrwałego początkowo są dostępne za pośrednictwem interfejsu REST, .NET / C#, interfejsy programu PowerShell i interfejsu wiersza polecenia.

Aby uzyskać ogólne informacje znajdują się odwołania ich, aby uzyskać więcej informacji, [magazynu kluczach](https://docs.microsoft.com/azure/key-vault/).

## <a name="scenarios"></a>Scenariusze

Usługa Azure Key Vault są śledzone zasoby, zarządzane przez usługę Azure Resource Manager. Usługa Azure Resource Manager określa również dobrze zdefiniowanych zachowanie do usunięcia, które wymaga pomyślnego uruchomienia operacji DELETE musi spowodować tego zasobu nie nie będą już dostępne. Funkcja usuwania nietrwałego adresów Odzyskiwanie usuniętego obiektu, czy został usunięty, przypadkowym lub zamierzone.

1. W typowym scenariuszu użytkownik może przypadkowo usunięta magazynu kluczy lub obiektem usługi key vault Jeśli, klucz magazynu lub klucza obiektu magazynu był możliwy do odzyskania wcześniej okres, użytkownik może cofnąć i odzyskać dane.

2. W przypadku różnych złośliwy użytkownik może próbować usunąć magazyn kluczy lub obiekt magazynu kluczy, takie jak klucz w magazynie, aby spowodować zakłócenia działania firmy. Oddzielenie usunięcia obiektu magazynu kluczy lub kluczy od rzeczywistego usunięcia danych bazowych może służyć jako środka bezpieczeństwa przez użytkownika, na przykład ograniczenie uprawnień do usunięcia danych z innym zaufany roli. Takie podejście wymaga skutecznie kworum dla danej operacji, które w przeciwnym razie może spowodować utratę natychmiastowe danych.

### <a name="soft-delete-behavior"></a>Zachowanie dotyczące usuwania nietrwałego

Za pomocą tej funkcji operacja usunięcia usługi key vault lub obiektu usługi key vault jest usuwania nietrwałego, efektywnie zawierającą te zasoby, dla okresu przechowywania danego (90 dni), zapewniając wygląd, że obiekt zostanie usunięta. Dodatkowo usługa zapewnia mechanizm odzyskiwania usuniętego obiektu, zasadniczo Cofnięcie usunięcia. 

Opcji soft-delete jest opcjonalne zachowanie usługi Key Vault i jest **nie jest włączona domyślnie** w tej wersji. 

### <a name="purge-protection--flag"></a>Wyczyść flagę ochrony
Przeczyść ochrony (**— ochrona przed czyszczeniem Włącz** w interfejsie wiersza polecenia platformy Azure) flaga jest domyślnie wyłączona. Gdy ta flaga jest włączona, magazynu lub obiektu w stanie usunięty, nie można przeczyścić aż do minął okres przechowywania 90 dni. Nadal można odzyskać taki magazyn lub obiektu. Ta flaga zapewnia dodano dla klientów, magazynu lub obiektu można nigdy trwale usunąć, dopóki nie upłynął okres przechowywania. Flaga ochrony przeczyszczania można włączyć tylko wtedy, gdy flaga opcji soft-delete znajduje się na lub podczas tworzenia magazynu włączyć obu opcji soft-delete i przeczyścić ochrony.

> [!NOTE] 
   Warunek wstępny do włączania ochrona przed czyszczeniem to musi mieć włączone usuwanie nietrwałe.
To polecenie, aby to zrobić w wersji 2 interfejsu wiersza polecenia platformy Azure

```
az keyvault create --name "VaultName" --resource-group "ResourceGroupName" --location westus --enable-soft-delete true --enable-purge-protection true
```

### <a name="permitted-purge"></a>Dozwolone przeczyszczania

Trwałe usuwanie przeczyszczanie, magazyn kluczy jest możliwe za pośrednictwem operacji POST, w zasobie serwer proxy i wymaga specjalnych uprawnień. Ogólnie rzecz biorąc tylko właściciel subskrypcji będzie można przeczyścić magazynu kluczy. Operację POST wyzwala natychmiastową i nieodwracalny usunięcie tego magazynu. 

Są wyjątki od tej reguły
- tak w przypadku subskrypcji platformy Azure została oznaczona jako *nieusuwalnej*. W tym przypadku jedyną usługą następnie może wykonać rzeczywiste usunięcie i odbywa się to jako proces w zaplanowanym. 
- Gdy — ochrona przed czyszczeniem Włącz flaga jest włączona na sam magazyn. W tym przypadku usługi Key Vault będzie czekać przez 90 dni od, gdy oryginalny obiekt tajny został oznaczony do usunięcia trwale usunąć obiekt.

### <a name="key-vault-recovery"></a>Odzyskiwanie usługi Key vault

Po usunięciu magazynu kluczy, usługa umożliwia utworzenie zasobu serwera proxy w ramach subskrypcji, dodając metadanych wystarczające do odzyskania. Zasób serwera proxy jest przechowywany obiekt dostępne w tej samej lokalizacji co usunięty magazyn kluczy. 

### <a name="key-vault-object-recovery"></a>Odzyskiwanie obiektów magazynu kluczy

Podczas usuwania obiektów magazynu kluczy, takie jak klucz usługi spowoduje umieszczenie obiektu w stanie usunięty, co czyni niedostępny do wszelkich operacji pobierania. W tym stanie obiektu magazynu kluczy może wystąpić tylko, odzyskana lub wymuszone/trwale usunięte. 

W tym samym czasie usługi Key Vault będą planować usunięcie danych bazowych, odpowiadający usunięty magazyn kluczy lub obiektu usługi key vault do wykonania po upływie czasu przechowywania wcześniej. Rekord DNS odpowiadający magazynu również są przechowywane przez czas trwania okresu przechowywania.

### <a name="soft-delete-retention-period"></a>Okres przechowywania opcji soft-delete

Nietrwale usunięte zasoby są przechowywane przez określony okres czasu, 90 dni. Podczas interwał przechowywania opcji soft-delete następujących warunków:

- Użytkownik może wyświetlić listę wszystkich magazynów kluczy i obiektów magazynu kluczy w stanie usuwania nietrwałego dla Twojej subskrypcji, a także dostęp do usuwania i odzyskiwania informacji o nich.
    - Tylko użytkownicy z uprawnieniami specjalnymi można wyświetlić listę usuniętych magazynów. Zaleca się, naszym użytkownikom tworzenie roli niestandardowej z odpowiednimi uprawnieniami specjalne magazyny obsługi usunięte.
- Nie można utworzyć magazyn kluczy o takiej samej nazwie w tej samej lokalizacji; odpowiednio magazyn kluczy nie można utworzyć obiektu w danym magazynie Jeśli tego magazynu kluczy zawiera obiekt o takiej samej nazwie i który jest w stanie usunięty 
- Tylko specjalnie użytkownikiem może przywrócić magazyn kluczy lub kluczy obiektu, wydając polecenie odzyskiwanie na odpowiadający jej zasób serwera proxy.
    - Użytkownik, członkiem niestandardowej roli, kto ma uprawnienia do tworzenia magazynu kluczy w grupie zasobów można przywrócić magazynu.
- Tylko specjalnie użytkownikiem może wymusić usuwanie magazynu kluczy lub obiektu usługi key vault wydając polecenie delete na odpowiadający jej zasób serwera proxy.

O ile nie zostanie przywrócona kluczy lub kluczy obiektu, usługę na końcu okresu przechowywania wykonuje Przeczyść usunięty nietrwale magazyn kluczy lub kluczy obiektu i jego zawartości. Usunięcie zasobu nie może być zaplanowane ponownie.

### <a name="billing-implications"></a>Opłaty wpływ

Ogólnie rzecz biorąc, gdy obiekt (magazyn kluczy lub klucz lub klucz tajny) jest w stanie usunięty, istnieją tylko dwa operacje możliwe: "Wyczyść" i "Odzyskiwanie". Wszystkie pozostałe operacje zakończy się niepowodzeniem. W związku z tym mimo że obiekt istnieje, nie można wykonywać operacji i dlatego nie użycia nastąpi, więc nie rachunku. Jednak postępujesz zgodnie z wyjątkami:

- Akcje "Wyczyść" i "Odzyskiwanie" są zliczane operacje normalnego magazynu kluczy i będą naliczane.
- Jeśli obiekt jest klucz HSM, opłat "Migracja klucza chronionego przez moduł HSM" na wersję klucza opłaty miesięcznej za zastosuje Jeśli wersja klucza został użyty w ciągu ostatnich 30 dni. Po tym, ponieważ obiekt jest w stanie usunięty, które można wykonywać żadnych operacji na, więc nie będzie naliczana opłata.

## <a name="next-steps"></a>Kolejne kroki

Następujące przewodniki dwóch oferują scenariuszy użycia podstawowego dla przy użyciu opcji soft-delete.

- [Jak używać usuwania nietrwałego w usłudze Key Vault z programem PowerShell](key-vault-soft-delete-powershell.md) 
- [Jak używać usuwania nietrwałego w usłudze Key Vault z interfejsem wiersza polecenia](key-vault-soft-delete-cli.md)

