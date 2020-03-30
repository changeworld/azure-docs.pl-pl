---
title: Model aplikacji sieci szkieletowej usług Azure
description: Jak modelować i opisywać aplikacje i usługi w sieci szkieletowej usług Azure przy użyciu plików manifestu aplikacji i usług.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 7179686b7d4ef2df267cb95ece8f83d5fb7682b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551883"
---
# <a name="model-an-application-in-service-fabric"></a>Modelowanie aplikacji w sieci szkieletowej usług
Ten artykuł zawiera omówienie modelu aplikacji usługi Azure Service Fabric i jak zdefiniować aplikację i usługę za pomocą plików manifestu.

## <a name="understand-the-application-model"></a>Opis modelu aplikacji
Aplikacja jest kolekcją usług składowych, które wykonują określone funkcje lub funkcje. Usługa wykonuje pełną i autonomiczną funkcję i może uruchamiać i uruchamiać niezależnie od innych usług.  Usługa składa się z kodu, konfiguracji i danych. Dla każdej usługi kod składa się z plików binarnych wykonywalnych, konfiguracja składa się z ustawień usługi, które mogą być ładowane w czasie wykonywania, a dane składają się z dowolnych danych statycznych, które mają być używane przez usługę. Każdy składnik w tym hierarchicznym modelu aplikacji można wersjonować i uaktualniać niezależnie.

![Model aplikacji sieci szkieletowej usług][appmodel-diagram]

Typ aplikacji jest kategoryzacją aplikacji i składa się z pakietu typów usług. Typ usługi jest kategoryzacją usługi. Kategoryzacja może mieć różne ustawienia i konfiguracje, ale podstawowe funkcje pozostają takie same. Wystąpienia usługi są różne odmiany konfiguracji usługi tego samego typu usługi.  

Klasy (lub "typy") aplikacji i usług są opisane za pomocą plików XML (manifesty aplikacji i manifesty usług).  Manifesty opisują aplikacje i usługi i są szablonami, dla których można utworzyć wystąpienia aplikacji z magazynu obrazów klastra.  Manifesty są szczegółowo opisane w [manifestach aplikacji i usług](service-fabric-application-and-service-manifests.md). Definicja schematu pliku ServiceManifest.xml i ApplicationManifest.xml jest instalowana z zestawem SDK sieci szkieletowej usług i narzędziami do *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. Schemat XML jest udokumentowany w [dokumentacji schematu ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

Kod dla różnych wystąpień aplikacji są uruchamiane jako oddzielne procesy, nawet jeśli są obsługiwane przez ten sam węzeł sieci szkieletowej usług. Ponadto cykl życia każdego wystąpienia aplikacji można zarządzać (na przykład uaktualniony) niezależnie. Na poniższym diagramie przedstawiono, jak typy aplikacji składają się z typów usług, które z kolei składają się z pakietów kodu, konfiguracji i danych. Aby uprościć diagram, `ServiceType4` wyświetlane są tylko pakiety kodu/config/data, chociaż każdy typ usługi będzie zawierał niektóre lub wszystkie te typy pakietów.

![Typy aplikacji sieci szkieletowej usług i typy usług][cluster-imagestore-apptypes]

Może istnieć jedno lub więcej wystąpień typu usługi aktywnych w klastrze. Na przykład wystąpienia usługi stanowej lub repliki osiągnąć wysoką niezawodność przez replikowanie stanu między replikami znajdujących się w różnych węzłach w klastrze. Replikacja zasadniczo zapewnia nadmiarowość dla usługi, które mają być dostępne, nawet jeśli jeden węzeł w klastrze nie powiedzie się. [Usługa podzielona na partycje](service-fabric-concepts-partitioning.md) dodatkowo dzieli swój stan (i wzorce dostępu do tego stanu) między węzłami w klastrze.

Na poniższym diagramie przedstawiono relację między aplikacjami i wystąpień usługi, partycje i repliki.

![Partycje i repliki w usłudze][cluster-application-instances]

> [!TIP]
> Układ aplikacji w klastrze można wyświetlić za pomocą narzędzia Eksploratora sieci szkieletowej usług dostępnego pod adresem http://&lt;yourclusteraddress&gt;:19080/Explorer. Aby uzyskać więcej informacji, zobacz [Wizualizacja klastra za pomocą Eksploratora sieci szkieletowej usług](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
- Dowiedz się więcej o [stanie](service-fabric-concepts-state.md)usługi, [partycjonowaniu](service-fabric-concepts-partitioning.md)i [dostępności](service-fabric-availability-services.md).
- Przeczytaj o tym, jak aplikacje i usługi są definiowane w [manifestach aplikacji i usług](service-fabric-application-and-service-manifests.md).
- [Modele hostingu aplikacji](service-fabric-hosting-model.md) opisują relację między replikami (lub wystąpieniami) wdrożonego procesu usługi i hosta usługi.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


