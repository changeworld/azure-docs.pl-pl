---
title: Ogólne wskazówki dotyczące zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight
description: Niektóre najlepsze rozwiązania, które powinny ułatwić wdrażanie i zarządzanie pakietem zabezpieczeń przedsiębiorstwa.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: be6c1fdc5deb6d541656c198469822dae0a5f7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463208"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Ogólne informacje i wskazówki dotyczące zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight

Podczas wdrażania bezpiecznego klastra HDInsight, istnieją pewne najlepsze rozwiązania, które powinny ułatwić wdrażanie i zarządzanie klastrem. Niektóre ogólne informacje i wytyczne są omówione tutaj.

## <a name="use-of-secure-cluster"></a>Korzystanie z bezpiecznego klastra

### <a name="recommended"></a>Zalecane

* Klaster będzie używany przez wielu użytkowników w tym samym czasie.
* Użytkownicy mają różne poziomy dostępu do tych samych danych.

### <a name="not-necessary"></a>Nie jest konieczne

* Będziesz uruchamiać tylko zadania automatyczne (takie jak konto pojedynczego użytkownika), standardowy klaster jest wystarczająco dobry.
* Importowanie danych można wykonać przy użyciu standardowego klastra i używać tego samego konta magazynu w innym bezpiecznym klastrze, w którym użytkownicy mogą uruchamiać zadania analizy.

## <a name="use-of-local-account"></a>Korzystanie z konta lokalnego

* Jeśli korzystasz z udostępnionego konta użytkownika lub konta lokalnego, trudno będzie określić, kto użył konta do zmiany konfiguracji lub usługi.
* Korzystanie z kont lokalnych jest problematyczne, gdy użytkownicy nie są już częścią organizacji.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Zasady

* Domyślnie Ranger używa **Deny** jako zasady.

* Gdy dostęp do danych odbywa się za pośrednictwem usługi, w której autoryzacja jest włączona:
  * Wtyczka autoryzacji Ranger jest wywoływana i biorąc pod uwagę kontekst żądania.
  * Ranger stosuje zasady skonfigurowane dla usługi. Jeśli zasady Ranger nie powiodą się, sprawdzanie dostępu zostanie odroczone do systemu plików. Niektóre usługi, takie jak MapReduce tylko sprawdzić, czy plik / folder jest własnością tego samego użytkownika, który przesyła żądanie. Usługi takie jak Hive, sprawdź, czy są zgodne`rwx`z własnością lub odpowiednie uprawnienia systemu plików ( ).

* Dla Hive, oprócz posiadania uprawnień do tworzenia / aktualizacji / usuwania `rwx`uprawnień, użytkownik powinien mieć uprawnienia do katalogu w magazynie i wszystkich podkatasi.

* Zasady mogą być stosowane do grup (preferowanych) zamiast do osób.

* Autoryzujący Ranger oceni wszystkie zasady Ranger dla tej usługi dla każdego żądania. Ta ocena może mieć wpływ na czas, aby zaakceptować zadanie lub kwerendę.

### <a name="storage-access"></a>Dostęp do pamięci masowej

* Jeśli typem magazynu jest WASB, nie jest zaangażowany token OAuth.
* Jeśli Ranger wykonał autoryzację, dostęp do magazynu odbywa się przy użyciu tożsamości zarządzanej.
* Jeśli Ranger nie wykonać żadnej autoryzacji, a następnie dostęp do magazynu odbywa się przy użyciu tokenu OAuth użytkownika.

### <a name="hierarchical-name-space"></a>Hierarchiczna przestrzeń nazw

Gdy hierarchiczne miejsce na nazwy nie jest włączone:

* Nie ma żadnych uprawnień dziedziczonych.
* Tylko uprawnienie systemu plików, które działa, to rola **RBAC danych magazynu XXXX,** która ma być przypisana do użytkownika bezpośrednio w witrynie Azure portal.

### <a name="default-hdfs-permissions"></a>Domyślne uprawnienia HDFS

* Domyślnie użytkownicy nie mają dostępu **/** do folderu w usłudze HDFS (muszą znajdować się w roli właściciela obiektu blob magazynu, aby uzyskać dostęp do pomyślnego).
* W przypadku katalogu przemieszczania dla mapreduce i innych, katalog `sticky _wx` specyficzny dla użytkownika jest tworzony i pod warunkiem uprawnień. Użytkownicy mogą tworzyć pliki i foldery pod spodem, ale nie mogą patrzeć na inne elementy.

### <a name="url-auth"></a>Auth adresu URL

Jeśli auth url jest włączony:

* Config będzie zawierać, jakie prefiksy są objęte `adl://`w auth url (jak ).
* Jeśli dostęp jest dla tego adresu URL, a następnie Ranger sprawdzi, czy użytkownik znajduje się na liście dozwolonych.
* Ranger nie sprawdzi żadnej z zasad drobnoziarnistych.

## <a name="resource-groups"></a>Grupy zasobów

Użyj nowej grupy zasobów dla każdego klastra, aby można było rozróżnić zasoby klastra.

## <a name="nsgs-firewalls-and-internal-gateway"></a>Sieciowe sieciowe, zapory sieciowe i brama wewnętrzna

* Użyj sieciowych grup zabezpieczeń (NSG), aby zablokować sieci wirtualne.
* Użyj zapory do obsługi zasad dostępu wychodzącego.
* Użyj bramy wewnętrznej, która nie jest otwarta dla publicznego internetu.

## <a name="azure-active-directory"></a>Usługa Azure Active Directory

[Usługa Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) to chmurowa usługa zarządzania tożsamościami i dostępami firmy Microsoft.

### <a name="policies"></a>Zasady

* Wyłącz zasady dostępu warunkowego przy użyciu zasad opartych na adresie IP. Wymaga to włączenia punktów końcowych usługi na sieci wirtualnych, w których są wdrażane klastry. Jeśli korzystasz z usługi zewnętrznej dla usługi MFA (innej niż usługa AAD), zasady oparte na adresach IP nie będą działać

* `AllowCloudPasswordValidation`jest wymagana dla użytkowników federowanych. Ponieważ usługa HDInsight używa nazwy użytkownika/ hasła bezpośrednio do uzyskania tokenów z usługi Azure AD, ta zasada musi być włączona dla wszystkich użytkowników federowanych.

* Włącz punkty końcowe usługi, jeśli wymagane jest obejście dostępu warunkowego przy użyciu zaufanych usług IP.

### <a name="groups"></a>Grupy

* Zawsze wdrażaj klastry z grupą.
* Użyj usługi Azure AD do zarządzania członkostwem w grupach (łatwiejsze niż próby zarządzania poszczególnych usług w klastrze).

### <a name="user-accounts"></a>Konta użytkowników

* Użyj unikatowego konta użytkownika dla każdego scenariusza. Na przykład użyj konta do importu, użyj innego do kwerendy lub innych zadań przetwarzania.
* Użyj zasad Ranger opartych na grupach zamiast poszczególnych zasad.
* Mieć plan, jak zarządzać użytkownikami, którzy nie powinni mieć już dostępu do klastrów.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Usługi domenowe Usługi Active Directory (Azure](../../active-directory-domain-services/overview.md) AD DS) zapewniają usługi domeny zarządzanej, takie jak dołączanie do domeny, zasady grupy, protokół LDAP (Lightweight Directory Access Protocol) i uwierzytelnianie Kerberos / NTLM, które jest w pełni zgodne z usługą Active Directory systemu Windows Server.

Usługi Azure AD DS jest wymagane dla bezpiecznych klastrów do przyłączenia się do domeny.
Usługa HDInsight nie może zależeć od lokalnych kontrolerów domeny ani niestandardowych kontrolerów domeny, ponieważ wprowadza zbyt wiele punktów błędów, udostępniania poświadczeń, uprawnień DNS itd. Aby uzyskać więcej informacji, zobacz Często zadawane pytania [dotyczące usług Azure AD DS](../../active-directory-domain-services/faqs.md).

### <a name="azure-ad-ds-instance"></a>Wystąpienie usług Ad DS usługi Azure

* Utwórz wystąpienie `.onmicrosoft.com domain`za pomocą pliku . W ten sposób nie będzie wielu serwerów DNS obsługujących domenę.
* Utwórz certyfikat z podpisem własnym dla usługi LDAPS i przekaż go do usługi Azure AD DS.
* Użyj sieci wirtualnej równorzędnej do wdrażania klastrów (jeśli masz wiele zespołów wdrażających klastry USŁUGI HDInsight ESP, będzie to przydatne). Dzięki temu nie trzeba otwierać portów (NSG) w sieci wirtualnej za pomocą kontrolera domeny.
* Poprawnie skonfiguruj system DNS dla sieci wirtualnej (nazwa domeny usług Azure AD DS powinna zostać rozwiązana bez żadnych wpisów pliku hosts).
* Jeśli ograniczasz ruch wychodzący, upewnij się, że przeczytałeś [przez obsługę zapory w umiaśnieniu HDInsight](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Właściwości zsynchronizowane z usługi Azure AD do usług Azure AD DS

* Synchronizacja połączenia usługi Azure AD z lokalnego do usługi Azure AD.
* Synchronizacja usług Ad DS platformy Azure z usługi Azure AD.

Okresowo usługi Azure AD DS synchronizuje obiekty z usługi Azure AD. Bloku usług AD DS w witrynie Azure Portal wyświetla stan synchronizacji. Na każdym etapie synchronizacji unikatowe właściwości mogą wpaść w konflikt i zmienić jego nazwę. Zwróć uwagę na mapowanie właściwości z usługi Azure AD na usługi Azure AD DS.

Aby uzyskać więcej informacji, zobacz [Populacja użytkownika usługi Azure ADPrincipalName](../../active-directory/hybrid/plan-connect-userprincipalname.md)i jak działa [synchronizacja usług Azure AD DS](../../active-directory-domain-services/synchronization.md).

### <a name="password-hash-sync"></a>Synchronizacja skrótu hasła

* Hasła są synchronizowane inaczej niż inne typy obiektów. Tylko nie odwracalne skróty haseł są synchronizowane w usługach Azure AD i usługach Azure AD DS
* Lokalnie do usługi Azure AD musi być włączona za pośrednictwem usługi AD Connect
* Synchronizacja usług Ad do usługi Azure AD DS jest automatyczna (opóźnienia są poniżej 20 minut).
* Skróty haseł są synchronizowane tylko wtedy, gdy jest zmienione hasło. Po włączeniu synchronizacji skrótów haseł wszystkie istniejące hasła nie są synchronizowane automatycznie, ponieważ są przechowywane nieodwracalnie. Po zmianie hasła skróty haseł są synchronizowane.

### <a name="computer-objects-location"></a>Lokalizacja obiektów komputera

Każdy klaster jest skojarzony z pojedynczą jednostką organizacyjną. Użytkownik wewnętrzny jest aprowizowana w o.o. Wszystkie węzły są domeną przyłączone do tej samej grupy organizacyjnej.

### <a name="active-directory-administrative-tools"></a>Narzędzia administracyjne usługi Active Directory

Aby uzyskać instrukcje dotyczące instalowania narzędzi administracyjnych usługi Active Directory na maszynie wirtualnej systemu Windows Server, zobacz [Instalowanie narzędzi do zarządzania](../../active-directory-domain-services/tutorial-create-management-vm.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="cluster-creation-fails-repeatedly"></a>Tworzenie klastra kończy się niepowodzeniem wielokrotnie

Najczęstsze przyczyny:

* Konfiguracja DNS nie jest poprawna, sprzężenie domeny węzłów klastra nie powiedzie się.
* NsGs są zbyt restrykcyjne, uniemożliwiając sprzężenie do domeny.
* Tożsamość zarządzana nie ma wystarczających uprawnień.
* Nazwa klastra nie jest unikatowa dla pierwszych sześciu znaków (w innym klastrze na żywo lub z usuniętym klastrem).

## <a name="next-steps"></a>Następne kroki

* [Konfiguracje pakietów zabezpieczeń przedsiębiorstwa z usługami domenowymi Active Directory platformy Azure w usłudze HDInsight](./apache-domain-joined-configure-using-azure-adds.md)

* [Synchronizowanie użytkowników usługi Azure Active Directory z klastrem USŁUGI HDInsight](../hdinsight-sync-aad-users-to-cluster.md).
