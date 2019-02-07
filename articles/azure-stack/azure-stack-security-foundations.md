---
title: Zrozumienie kontroli zabezpieczeń usługi Azure Stack
description: Jako administrator usługi Dowiedz się więcej o kontroli bezpieczeństwa do usługi Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: efa97c18a63954239475338c85f2145b8c6c6ac6
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767273"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Poziom zabezpieczeń infrastruktury w usłudze Azure Stack

*Dotyczy: Zintegrowane systemy usługi Azure Stack*

Zagadnienia dotyczące zabezpieczeń i przepisy dotyczące zgodności są głównym sterowniki przy użyciu chmury hybrydowej. Usługa Azure Stack jest przeznaczona dla tych scenariuszy. W tym artykule opisano kontroli bezpieczeństwa na potrzeby usługi Azure Stack.

Współistnienia dwóch warstw ogólny poziom zabezpieczeń w usłudze Azure Stack. Pierwsza warstwa jest infrastruktury Azure Stack, która obejmuje składniki sprzętowe do usługi Azure Resource Manager. Pierwsza warstwa obejmuje administratora i portale dzierżawy. Druga warstwa składa się z obciążeń utworzone, wdrażania i zarządzania nimi przez dzierżawców. Druga warstwa zawiera elementy, takie jak maszyny wirtualne i usługi aplikacji, witryn sieci web.

## <a name="security-approach"></a>Podejście do zabezpieczeń

Poziom zabezpieczeń dla usługi Azure Stack jest przeznaczona do obrony przed nowoczesnymi zagrożeniami i została skompilowana do spełnienia wymagań z głównych standardami. W rezultacie ogólny poziom zabezpieczeń infrastruktury Azure Stack w oparciu dwóch filarach:

 - **Założenie złamania zabezpieczeń**  
Począwszy od przy założeniu, że system już został naruszony, skupić się na *wykrywanie i ograniczając wpływ naruszeń* a tylko chcesz uniemożliwić ataki. 
 - **Wzmocnione domyślnie**  
Ponieważ infrastruktura działa dobrze zdefiniowane sprzęt i oprogramowanie, usługi Azure Stack *umożliwia, konfiguruje i weryfikuje wszystkie funkcje zabezpieczeń* domyślnie.

Ponieważ usługi Azure Stack jest dostarczana jako system zintegrowany, poziom zabezpieczeń infrastruktury Azure Stack jest zdefiniowana przez firmę Microsoft. Podobnie jak na platformie Azure, dzierżawców są odpowiedzialne za definiowanie stan zabezpieczeń obciążeń dzierżawcy. Ten dokument zawiera wiedzy fundamentalnych na poziom zabezpieczeń infrastruktury Azure Stack.

## <a name="data-at-rest-encryption"></a>Dane szyfrowanie rest
Wszystkie usługi Azure Stack infrastruktury i dzierżawcy dane są szyfrowane w stanie spoczynku przy użyciu funkcji BitLocker. To szyfrowanie zapewnia ochronę względem fizycznej utraty lub kradzieży składników magazynu usługi Azure Stack. Aby uzyskać więcej informacji, zobacz [danych rest szyfrowanie w usłudze Azure Stack](azure-stack-security-bitlocker.md).

## <a name="data-in-transit-encryption"></a>Dane przesyłane szyfrowania
Składniki infrastruktury usługi Azure Stack komunikacji za pomocą kanałów zaszyfrowany przy użyciu protokołu TLS 1.2. Certyfikaty szyfrowania własnym są zarządzane przez infrastrukturę. 

Wszystkie punkty końcowe infrastruktury zewnętrznych, takich jak punkty końcowe REST lub portalu usługi Azure Stack obsługi protokołu TLS 1.2 w celu zagwarantowania bezpiecznej komunikacji. Certyfikaty szyfrowania, z innych firm lub urzędu certyfikacji przedsiębiorstwa, należy określić dla tych punktów końcowych. 

Chociaż mogą być używane certyfikaty z podpisem własnym dla tych zewnętrznych punktów końcowych, Microsoft zdecydowanie z informacją o tym przed ich używania. 

## <a name="secret-management"></a>Zarządzanie wpisami tajnymi
Infrastruktura usługi Azure Stack używa wpisy tajne, takie jak hasła, dlatego też działać. Większość z nich są automatycznie obracane często, ponieważ są one konta Group-Managed usług, które Obróć co 24 godziny.

Pozostałe wpisy tajne, które nie są kontami usług Group-Managed można obracać ręcznie przy użyciu skryptu w uprzywilejowanych punktu końcowego.

## <a name="code-integrity"></a>Integralność kodu
Usługa Azure Stack, korzysta z najnowszych systemu Windows Server 2016 funkcji zabezpieczeń. Jeden z nich jest systemu Windows Defender funkcji Device Guard, zawiera listę dozwolonych aplikacji, która zapewnia, że tylko autoryzowani kod działa w ramach infrastruktury Azure Stack. 

Autoryzowanego kodu jest podpisany przez firmę Microsoft lub partnerem OEM. Podpisanego kodu autoryzowanych znajduje się na liście dozwolonych oprogramowania określone w zasadach zdefiniowana przez firmę Microsoft. Innymi słowy mogą być wykonywane tylko w przypadku oprogramowania, które zostało zatwierdzone do uruchomienia w ramach infrastruktury Azure Stack. Każda próba wykonania nieautoryzowanego kodu jest zablokowana i inspekcji jest wygenerowany.

Zasady funkcji Device Guard zapobiega także agentów innych firm lub oprogramowanie działające w infrastrukturze Azure Stack.

## <a name="credential-guard"></a>Credential Guard
Kolejną funkcją zabezpieczeń systemu Windows Server 2016 w usłudze Azure Stack jest program Windows Defender Credential Guard, który jest używany do ochrony poświadczeń infrastruktury Azure Stack z typu Pass--Hash i ataków Pass--Ticket.

## <a name="antimalware"></a>Oprogramowanie chroniące przed złośliwym kodem
Każdy składnik w usłudze Azure Stack (hosty funkcji Hyper-V i maszyn wirtualnych) jest chroniony za pomocą programu antywirusowego Windows Defender.

W połączonych scenariuszach aktualizacje definicji i aparatu oprogramowania antywirusowego są stosowane wiele razy dziennie. W scenariuszach bez połączenia aktualizacji ochrony przed złośliwym kodem są stosowane w ramach comiesięcznych aktualizacji usługi Azure Stack. Aby uzyskać więcej informacji, zobacz [aktualizacji programu antywirusowego Windows Defender w usłudze Azure Stack](azure-stack-security-av.md).

## <a name="constrained-administration-model"></a>Model administracyjny ograniczone
Administracja w usłudze Azure Stack jest kontrolowany za pośrednictwem trzech punktów wejścia, każdy z określonym przeznaczeniem: 
1. [Portal administratora](azure-stack-manage-portals.md) udostępnia środowisko wskaż i kliknij na potrzeby codziennych operacji zarządzania.
2. Usługa Azure Resource Manager udostępnia wszystkie operacje zarządzania z portalu administratora, interfejs API RST, używane przez program PowerShell i wiersza polecenia platformy Azure. 
3. Dla określonych operacji niskiego poziomu, na przykład data center integration lub obsługi scenariuszy, usługi Azure Stack uwidacznia punkt końcowy programu PowerShell, nazywane [uprzywilejowanych punktu końcowego](azure-stack-privileged-endpoint.md). Ten punkt końcowy ujawnia tylko zestaw dozwolonych poleceń cmdlet i intensywnie podlega inspekcji.

## <a name="network-controls"></a>Kontrolki sieci
Infrastruktura usługi Azure Stack jest dostarczany za pomocą różnych warstw sieciowych listy kontroli dostępu (ACL). Listy ACL zapobiegać nieautoryzowanemu dostępowi do składników infrastruktury i ograniczyć komunikacji infrastruktury do ścieżek, które są wymagane do jego funkcjonowania. 

Listy ACL sieci są wymuszane w trzech warstwach:
1.  Przełączniki tor
2.  Sieć definiowana przez oprogramowanie
3.  Zapory systemu operacyjnego hosta i maszyny Wirtualnej

## <a name="regulatory-compliance"></a>Zgodność z przepisami

Usługa Azure Stack zrealizowano formalne oceny przez firmę inspekcji niezależnie od innych firm. W rezultacie dokumentację dotyczącą jak infrastruktury Azure Stack spełnia dotyczy formanty z kilku standardów zgodności głównych jest dostępna. Dokumentacja nie jest to program certyfikacji usługi Azure Stack z powodu standardów, w tym kilka formantów z pracownikami i powiązane z procesem. Zamiast klienci mogą używać tej dokumentacji, aby szybko rozpocząć tworzenie swoich procesu certyfikacji.

Oceny obejmują następujące standardy:

- [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) adresy branży kart płatniczych.
- [Macierzy kontroli chmury CSA](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) to kompleksowe mapowania przez wiele standardów, w tym FedRAMP umiarkowany, ISO27001, HIPAA, HITRUST, ITAR, NIST SP800-53 i inne.
- [Programowi FedRAMP High](https://www.fedramp.gov/fedramp-releases-high-baseline/) dla klientów rządowych.

Dokumentacja zgodności znajduje się na [Portal zaufania usługi Microsoft](https://servicetrust.microsoft.com/ViewPage/Blueprint). Przewodniki dotyczące zgodności są zasobem chronionym i wymagają, możesz zalogować się przy użyciu poświadczeń usługi platformy Azure w chmurze.

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się, jak wymienić klucze tajne w usłudze Azure Stack](azure-stack-rotate-secrets.md)
- [PCI-DSS i CSA-CCM dokumentów dla usługi Azure Stack](https://servicetrust.microsoft.com/ViewPage/TrustDocuments)
- [Departament Obrony i NIST dokumentów dla usługi Azure Stack](https://servicetrust.microsoft.com/ViewPage/Blueprint)
