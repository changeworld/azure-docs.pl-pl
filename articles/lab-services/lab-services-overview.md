---
title: Informacje o usłudze Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak usługa Lab Services może ułatwić tworzenie i zabezpieczanie laboratoriów z maszynami wirtualnymi, które mogą być używane przez deweloperów, testerów, nauczycieli, uczniów itd., a także zarządzanie tymi laboratoriami.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/13/2018
ms.author: spelluru
ms.openlocfilehash: 2f4bc3bc00d1a803ed678e49df23a78e6b063e50
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957797"
---
# <a name="an-introduction-to-azure-lab-services"></a>Wprowadzenie do usługi Azure Lab Services

Usługa Azure Lab Services pozwala szybko skonfigurować środowisko dla zespołu (na przykład środowisko deweloperskie, środowisko testowe lub środowisko laboratorium na potrzeby zajęć) w chmurze. Właściciel laboratorium tworzy laboratorium, aprowizuje maszyny wirtualne z systemem Windows lub Linux, instaluje niezbędne oprogramowanie i narzędzia oraz udostępnia je użytkownikom laboratorium. Użytkownicy laboratorium łączą się z maszynami wirtualnymi w laboratorium, a następnie używają ich w codziennej pracy lub krótkoterminowych projektach albo do wykonywania ćwiczeń w klasie. Gdy użytkownicy rozpoczną korzystanie z zasobów w laboratorium, administrator laboratorium może analizować koszty i użycie w wielu laboratoriach, a następnie ustawić nadrzędne zasady w celu zoptymalizowania kosztów organizacji lub zespołu.

> [!IMPORTANT]
> Usługa **Azure DevTest Labs** jest rozszerzana o nowe typy laboratoriów (Azure Lab Services)!
>  
> Usługa Azure Lab Services umożliwia tworzenie laboratoriów zarządzanych, takich jak laboratoria na potrzeby zajęć. Sama usługa obsługuje całe zarządzanie infrastrukturą w laboratoriach zarządzanych, od inicjowania maszyn wirtualnych do obsługi błędów i skalowania infrastruktury. Laboratoria zarządzane są obecnie w wersji zapoznawczej. Po zakończeniu wersji zapoznawczej nowe typy laboratoriów i istniejące laboratoria usługi DevTest Labs będą wspólnie nazywane usługą Azure Lab Services i w jej ramach wszystkie typy laboratoriów będą dalej rozwijane.

## <a name="key-capabilities"></a>Najważniejsze możliwości

Usługa Azure Lab Services obsługuje następujące kluczowe możliwości/funkcje:

- **Szybka i elastyczna konfiguracja laboratorium**. Przy użyciu usługi Azure Lab Services właściciele laboratoriów mogą szybko skonfigurować laboratorium odpowiednio do swoich potrzeb. Usługa może zająć się całą pracą związaną z infrastrukturą platformy Azure w laboratoriach zarządzanych. Alternatywnie właściciele laboratoriów mogą samodzielnie zarządzać infrastrukturą i dostosowywać ją w swojej subskrypcji. Usługa zapewnia wbudowane skalowanie i odporność infrastruktury w laboratoriach zarządzanych przez usługę.
- **Uproszczone środowisko dla użytkowników laboratorium**. W laboratorium zarządzanym, takim jak laboratorium na potrzeby zajęć, użytkownicy laboratorium mogą się zarejestrować za pomocą kodu rejestracji, a następnie uzyskać dostęp do laboratorium w dowolnym czasie, aby korzystać z zasobów laboratorium. W laboratorium utworzonym w usłudze DevTest Labs właściciel laboratorium może nadać użytkownikom laboratorium uprawnienia do tworzenia maszyn wirtualnych i uzyskiwania do nich dostępu, ponownego używania dysków z danymi i zarządzania nimi oraz konfigurowania wpisów tajnych wielokrotnego użytku.  
- **Optymalizacja i analiza kosztów**. Właściciel laboratorium może ustawić harmonogramy laboratorium w celu automatycznego zamykania i uruchamiania maszyn wirtualnych. Właściciel laboratorium może ustawić harmonogram, aby określić przedziały czasu, gdy maszyny wirtualne laboratorium są dostępne dla użytkowników, ustawić zasady użytkowania dla poszczególnych użytkowników lub laboratoriów w celu optymalizacji kosztów oraz analizować trendy używania i działań w laboratorium. W laboratoriach zarządzanych, takich jak laboratoria na potrzeby zajęć, obecnie dostępny jest mniejszy podzbiór opcji optymalizacji i analizy kosztów.
- **Osadzone zabezpieczenia**. Właściciel laboratorium może skonfigurować prywatne sieci i podsieci wirtualne dla laboratorium oraz włączyć udostępniony publiczny adres IP. Użytkownicy laboratorium mogą bezpiecznie uzyskać dostęp do zasobów przy użyciu sieci wirtualnej skonfigurowanej za pomocą usługi ExpressRoute i sieci VPN typu lokacja–lokacja. (Obecnie są one dostępne tylko w usłudze DevTest Labs).
- **Integracja z przepływami pracy i narzędziami**. Usługa Azure Lab Services umożliwia integrację laboratoriów z systemami witryny internetowej i zarządzania organizacji. Możesz automatycznie aprowizować środowiska za pomocą narzędzi ciągłej integracji/ciągłego wdrażania. (Obecnie są one dostępne tylko w usłudze DevTest Labs).

> [!NOTE]
> Obecnie usługa Azure Lab Services obsługuje tylko maszyny wirtualne utworzone na podstawie obrazów z witryny Azure Marketplace. Jeśli chcesz użyć obrazów niestandardowych lub utworzyć inne zasoby usług PaaS w środowisku laboratoryjnym, użyj usługi DevTest Labs. Aby uzyskać więcej informacji, zobacz [Create a custom image in DevTest Labs (Tworzenie obrazu niestandardowego w usłudze DevTest Labs)](devtest-lab-create-custom-image-from-vm-using-portal.md) i [Create lab envionrments using Resource Manager templates (Tworzenie środowisk laboratoryjnych przy użyciu szablonów usługi Resource Manager)](devtest-lab-create-environment-from-arm.md).

## <a name="scenarios"></a>Scenariusze

Oto niektóre scenariusze obsługiwane przez usługę Azure Lab Services:

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Konfigurowanie laboratorium komputerowego o zmiennym rozmiarze w chmurze na potrzeby zajęć  

- Utwórz laboratorium zarządzane na potrzeby zajęć. Po prostu poinformuj usługę, czego dokładnie potrzebujesz, a ona utworzy infrastrukturę laboratorium i będzie nią zarządzać, dzięki czemu można będzie się skupić na nauczaniu, a nie na technicznych szczegółach laboratorium.
- Zapewnij uczniom laboratorium maszyn wirtualnych, na których skonfigurowano dokładnie to, co jest potrzebne na zajęciach. Przyznaj każdemu uczniowi ograniczoną liczbę godzin używania maszyn wirtualnych do pracy na zajęciach.  
- Przenieś fizyczne laboratorium komputerowe swojej szkoły do chmury. Automatycznie skaluj liczbę maszyn wirtualnych tylko do maksymalnego progu użycia i kosztów ustawionych dla laboratorium.
- Gdy skończysz pracę, usuń laboratorium jednym kliknięciem.

### <a name="use-devtest-labs-for-development-environments"></a>Używanie usługi DevTest Labs w środowiskach deweloperskich

Usługi Azure DevTest Labs można używać do wdrażania wielu kluczowych scenariuszy, ale jeden z podstawowych scenariuszy obejmuje używanie usługi DevTest Labs do hostowania maszyn deweloperskich dla deweloperów. W tym scenariuszu usługa DevTest Labs zapewnia następujące korzyści:

- Umożliwienie deweloperom szybkiej aprowizacji ich maszyn deweloperskich na żądanie.
- Aprowizacja środowiska systemu Windows i Linux za pomocą szablonów wielokrotnego użytku i artefaktów.
- Deweloperzy mogą łatwo dostosowywać swoje maszyny deweloperskie w miarę potrzeb.
- Administratorzy mogą kontrolować koszty, zapewniając, że deweloperzy nie mogą uzyskać więcej maszyn wirtualnych niż to niezbędne do programowania, a ponadto maszyny wirtualne są zamykane, gdy nie są używane.

Aby uzyskać więcej informacji, zobacz [Używanie usługi DevTest Labs do programowania](devtest-lab-developer-lab.md).

### <a name="use-devtest-labs-for-test-environments"></a>Korzystanie z usługi DevTest Labs na potrzeby środowisk testowych

Usługi Azure DevTest Labs możesz używać do wdrażania wielu kluczowych scenariuszy, ale podstawowy scenariusz polega na używaniu usługi DevTest Labs do hostowania maszyn dla testerów. W tym scenariuszu usługa DevTest Labs zapewnia następujące korzyści:

- Testerzy mogą przetestować najnowszą wersję swoich aplikacji dzięki możliwości szybkiej aprowizacji środowisk systemów Windows i Linux przy użyciu szablonów wielokrotnego użytku i artefaktów.
- Testerzy mogą skalować w górę swoje testowane obciążenie, aprowizując wielu agentów testowych.
- Administratorzy mogą kontrolować koszty, zapewniając, że testerzy nie mogą uzyskać więcej maszyn wirtualnych niż to niezbędne do testowania, a ponadto maszyny wirtualne są zamykane, gdy nie są używane.

Aby uzyskać więcej informacji, zobacz [Używanie usługi DevTest Labs do testowania](devtest-lab-test-env.md).

## <a name="user-profiles"></a>Profile użytkowników

W tym artykule opisano różne profile użytkowników w usłudze Azure Lab Services.

### <a name="lab-account-owner"></a>Właściciel konta laboratorium

Zazwyczaj administrator IT zasobów chmury organizacji, który jest właścicielem subskrypcji platformy Azure działa jako właściciel konta laboratorium i wykonuje następujące zadania:

- Konfiguruje konto laboratorium dla Twojej organizacji.
- Konfiguruje zasady dla wszystkich laboratoriów i zarządza nimi.
- Udziela osobom w organizacji uprawnień do tworzenia laboratorium w ramach konta laboratorium.

### <a name="lab-creator"></a>Twórca laboratorium

Zazwyczaj użytkownicy, tacy jak lider/kierownik programowania, nauczyciel, host maratonu programistycznego czy szkoleniowiec online, tworzą laboratoria w ramach konta laboratorium. Twórca laboratorium wykonuje następujące zadania:

- Tworzy laboratorium.
- Tworzy maszyny wirtualne w laboratorium.
- Instaluje odpowiednie oprogramowanie na maszynach wirtualnych.
- Określa, kto ma dostęp do laboratorium.
- Udostępnia link do laboratorium użytkownikom laboratorium.

### <a name="lab-user"></a>Użytkownik laboratorium

Użytkownik laboratorium wykonuje następujące zadania:

- Używa linku rejestracji, który otrzymuje od twórcy laboratorium w celu zarejestrowania się w laboratorium.
- Nawiązuje połączenie z maszyną wirtualną w laboratorium i używa jej do programowania, testowania i wykonywania prac na zajęciach.

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do konfigurowania laboratorium przy użyciu usługi Azure Lab Services:

- [Konfigurowanie laboratorium na potrzeby zajęć](classroom-labs/tutorial-setup-classroom-lab.md)
- [Konfigurowanie laboratorium](tutorial-create-custom-lab.md)
