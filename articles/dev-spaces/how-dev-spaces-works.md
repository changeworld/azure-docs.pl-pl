---
title: Jak działa usługa Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: W tym artykule opisano procesy, które zasilają usługi Azure Dev Spaces
keywords: Miejsca deweloperów platformy Azure, przestrzenie deweloperów, platforma do dokowania, sieci Kubernetes, platforma Azure, usługa AKS, usługa Azure Kubernetes, kontenery
ms.openlocfilehash: 99b0b3309d115b450bfca94871b6defd885349fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234951"
---
# <a name="how-azure-dev-spaces-works"></a>Jak działa usługa Azure Dev Spaces

Tworzenie aplikacji Kubernetes może być trudne. Potrzebne są pliki konfiguracyjne platformy Docker i Kubernetes. Musisz dowiedzieć się, jak przetestować aplikację lokalnie i interakcji z innymi usługami zależnymi. Może być konieczne obsługa tworzenia i testowania wielu usług jednocześnie i z zespołem deweloperów.

Usługa Azure Dev Spaces zapewnia wiele sposobów szybkiego iteracji i debugowania aplikacji Kubernetes i współpracy z zespołem. W tym artykule opisano, co usługa Azure Dev Spaces może zrobić i jak to działa.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Szybko iteracji i debugowania aplikacji Kubernetes

Usługa Azure Dev Spaces zmniejsza nakład pracy w celu opracowania, przetestowania i iteracji aplikacji Kubernetes w kontekście klastra AKS. To zmniejszenie nakładu pracy pozwala deweloperom skupić się na logice biznesowej swoich aplikacji i nie konfigurowania ich usług do uruchamiania w umięwersy.

### <a name="connect-your-development-machine-to-aks"></a>Podłączanie maszyny deweloperskiej do aksu

Za pomocą usługi Azure Dev Spaces można podłączyć komputer dewelopera do klastra AKS, umożliwiając uruchamianie i debugowanie kodu na komputerze deweloperskim, tak jakby był uruchomiony w klastrze. Usługa Azure Dev Spaces przekierowuje ruch między połączonym klastrem AKS, uruchamiając zasobnik w klastrze, który działa jako agent zdalny, aby przekierować ruch między komputerem deweloperskim a klastrem. To przekierowanie ruchu umożliwia kod na komputerze deweloperskim i usługach uruchomionych w klastrze AKS do komunikowania się tak, jakby były w tym samym klastrze AKS. Aby uzyskać więcej informacji na temat podłączania komputera deweloperskiego do usługi AKS, zobacz [Jak działa podłączanie komputera deweloperskiego do klastra AKS.][how-it-works-connect]

### <a name="run-your-code-in-aks"></a>Uruchamianie kodu w aks

Oprócz przekierowywania ruchu między komputerem deweloperskim a klastrem AKS, za pomocą usługi Azure Dev Spaces można skonfigurować i szybko uruchomić kod bezpośrednio w usłudze AKS. Dzięki programowi Visual Studio, visual studio code lub interfejsu wiersza polecenia azure dev spaces przestrzenie deweloperów przekażą kod do klastra, a następnie skompilują go i uruchomią. Przestrzenie deweloperów platformy Azure mogą również inteligentnie synchronizować zmiany kodu i ponownie uruchamiać usługę, aby w razie potrzeby odzwierciedlić zmiany. Podczas uruchamiania kodu dzienniki kompilacji i śledzenia HTTP są przesyłane strumieniowo z powrotem do klienta, dzięki czemu można monitorować postęp i diagnozować wszelkie problemy. Można również użyć usługi Azure Dev Spaces, aby dołączyć debuger w programie Visual Studio i Visual Studio Code do usług Java, Node.js i .NET Core. Aby uzyskać więcej informacji, zobacz [Jak działa przygotowanie projektu dla usługi Azure Dev Spaces][how-it-works-prep], Jak działa [uruchamianie kodu za pomocą usługi Azure Dev Spaces][how-it-works-up]i jak [zdalnie debugowanie kodu za pomocą usługi Azure Dev Spaces działa.][how-it-works-remote-debugging]

## <a name="team-development"></a>Programowanie zespołowe

Usługa Azure Dev Spaces pomaga zespołom wydajnie pracować nad aplikacją w tym samym klastrze AKS bez zakłócania pracy.

### <a name="intelligent-routing-between-dev-spaces"></a>Inteligentne wyznaczanie tras między przestrzeniami deweloperskimi

Za pomocą usługi Azure Dev Spaces zespół może udostępniać pojedynczy klaster AKS z uruchomioną aplikacją natywną w chmurze i tworzyć pojedyncze przestrzenie deweloperów, w których zespół może tworzyć, testować i debugować bez zakłócania innych przestrzeni deweloperskich. Wersja bazowa aplikacji jest uruchamiana w głównym obszarze deweloperskim. Członkowie zespołu następnie utworzyć niezależne przestrzenie dewelopera podrzędnego na podstawie miejsca głównego dla rozwoju, testowania i debugowania zmian w aplikacji. Za pośrednictwem funkcji routingu w przestrzeni deweloperów podrzędnych przestrzeni deweloperskich można kierować żądania między usługami działającymi w przestrzeni deweloperów podrzędnych i nadrzędnego obszaru dev. Ta routing umożliwia deweloperom uruchamianie własnej wersji usługi podczas ponownego stosowania usług zależnych z obszaru nadrzędnego. Każda przestrzeń podrzędna ma swój własny unikatowy adres URL, który może być udostępniany i uzyskiwać do nich dostęp przez inne osoby w celu współpracy. Aby uzyskać więcej informacji na temat działania routingu w usłudze Azure Dev Spaces, zobacz [Jak routing działa z usługą Azure Dev Spaces.][how-it-works-routing]

### <a name="live-testing-an-open-pull-request"></a>Testowanie na żywo otwartego żądania ściągania

Akcje usługi GitHub z usługą Azure Dev Spaces można również użyć do testowania zmian w aplikacji w żądaniu ściągania bezpośrednio w klastrze przed scaleniem. Usługa Azure Dev Spaces można automatycznie wdrożyć wersję przeglądu aplikacji do klastra, dzięki czemu autor, jak również innych członków zespołu do przeglądu zmian w kontekście całej aplikacji. Korzystając z możliwości routingu usługi Azure Dev Spaces, ta wersja przeglądu aplikacji jest również wdrażana w klastrze bez wpływu na inne przestrzenie deweloperów. Wszystkie te funkcje umożliwiają pewnie zatwierdzanie i scalanie żądań ściągania. Aby zobaczyć przykład akcji Usługi GitHub i usługi Azure Dev Spaces, zobacz [Akcje usługi GitHub & usłudze Azure Kubernetes][pr-flow].

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć podłączanie lokalnego komputera deweloperskiego do klastra AKS, zobacz [Łączenie komputera deweloperskiego z klastrem AKS][connect].

Aby rozpocząć korzystanie z usługi Azure Dev Spaces do tworzenia zespołów, zobacz tworzenie zespołu w przewodniku Szybki start [usługi Azure Dev Spaces.][quickstart-team]

[connect]: how-to/connect.md
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development