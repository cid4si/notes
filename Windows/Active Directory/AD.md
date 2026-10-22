# Guia do Active Directory

## Índice

<details>
    <summary>Índice</summary>

- [1. O que é Active Directory](#o-que-é-active-directory)
    - [1.1. Visão Geral](#visão-geral)
    - [1.2. Serviços do Active Directory](#serviços-do-active-directory)
    - [1.3. Componentes do Active Directory](#componentes-do-active-directory)
- [2. Estrutura do Active Directory](#estrutura-do-active-directory)
    - [2.1. Hierarquia: Florestas, Domínios e Unidades Organizacionais](#hierarquia-florestas-domínios-e-unidades-organizacionais)
        - [2.1.1. Florestas](#florestas)
        - [2.1.2. Domínios](#domínios)
        - [2.1.3. Árvores (Trees)](#árvores-trees)
        - [2.1.4. Unidades Organizacionais (OUs)](#unidades-organizacionais-ous)
    - [2.2. Componentes Físicos do Active Directory](#componentes-físicos-do-active-directory)
        - [2.2.1. Domain Controllers (DCs)](#domain-controllers-dcs)
        - [2.2.2. AD DS Data Store](#ad-ds-data-store)
        - [2.2.3. Configuração de Rede](#configuração-de-rede)
    - [2.3. Componentes Lógicos do Active Directory](#componentes-lógicos-do-active-directory)
        - [2.3.1. Schema do AD DS](#schema-do-ad-ds)
        - [2.3.2. Objetos](#objetos)
        - [2.3.3. Grupos](#grupos)
        - [2.3.4. Trusts (Confianças)](#trusts-confianças)
        - [2.3.5. Group Policy Objects (GPOs)](#group-policy-objects-gpos)
- [3. Kerberos](#kerberos)
- [4. Segurança e Exploração](#segurança-e-exploração)

</details>


## O que é Active Directory

O **Active Directory (AD)** é o serviço de diretório Microsoft mais utilizado no mundo. Ele gerencia e controla o acesso a recursos em redes de domínio Windows, armazenando tudo como **objetos**.

**Características principais:**
- Gerenciamento centralizado de identidades e acesso
- Autenticação via [**Kerberos**](#kerberos)
- Suporte a dispositivos não-Windows (Linux, firewalls, etc.) via **RADIUS** ou **LDAP**
- Model centralizado com **manageabilidade, segurança e interoperabilidade**

**Informações armazenadas:**

- **Usuários Windows:** contas, privilégios, perfis, políticas
- **Servidores:** configuração, informações de rede, impressoras, compartilhamentos, políticas
- **Clientes:** configuração, rede, políticas
- **Dispositivos de rede:** configuração, QoS, políticas de segurança
- **Firewalls:** configuração, VPN, políticas
- **Aplicações:** configuração, SSO, dados específicos
- **Servidores de email:** caixas de correio, catálogo de endereços
- **Outros diretórios:** registros, políticas de segurança

## Serviços do Active Directory

| Serviço | Função |
|:--------|:-------|
| **AD Domain Services (AD DS)** | Serviço principal; gerencia domínios Windows, autenticação e políticas de grupo. |
| **AD Rights Management Services (RMS)** | Protege informações contra uso não autorizado. Estabelece identidade do usuário e emite licenças para conteúdo protegido. |
| **AD Certificate Services (CS)** | Cria entidades de certificação (CA) e gerencia emissão de certificados para autenticação e criptografia. |
| **AD Federation Services (FS)** | Oferece federação segura com **Single Sign-On (SSO)** na web. Permite login único para acessar recursos como Azure ou Microsoft 365. |

## Componentes do AD

| Componente | Descrição |
|:-----------|:----------|
| **Schema** | Define a estrutura do diretório: tipos de objetos (usuários, computadores, grupos) e seus atributos. Atualizações são raras e requerem planejamento cuidadoso, pois afetam toda a estrutura. |
| **Query/Index** | Mecanismo eficiente de busca via **LDAP** (Lightweight Directory Access Protocol). Índices otimizam consultas frequentes (usuários, grupos, recursos). |
| **Global Catalog (GC)** | Réplica parcial e somente leitura de todos os objetos da floresta. Facilita buscas entre domínios, suporta logins em ambientes multi-domínio e acelera consultas globais. |
| **Replication Service** | Sincroniza mudanças entre domain controllers. Usa modelo **multi-master** (qualquer DC pode fazer alterações). Otimizado com sincronização delta para reduzir tráfego de rede. |

---

## Estrutura do Active Directory

### Hierarquia: Florestas, Domínios e Unidades Organizacionais

A estrutura do AD é organizada em níveis hierárquicos que definem limites administrativos e de segurança:

#### Florestas
A **floresta** é a estrutura de nível superior e o **limite de segurança final** do Active Directory.

**Características:**
- Consiste em um ou mais domínios que compartilham um **esquema comum**, **configuração** e **Catálogo Global**
- Relações de confiança entre domínios na mesma floresta são **automáticas e transitivas**
- Atua como o nível mais alto de isolamento — trusts entre florestas devem ser **explicitamente criados**
- Políticas e configurações de segurança definidas no nível da floresta afetam todos os domínios contidos
- Todos os domínios compartilham os grupos **Enterprise Admins** e **Schema Admins**

#### Domínios
Os **domínios** são subconjuntos dentro de uma floresta que servem como **limites administrativos**.

**Características:**
- Cada domínio possui suas próprias **políticas e serviços de autenticação únicos**
- Compartilham o **esquema e configuração** da floresta
- Contêm usuários, computadores, grupos e outros objetos
- Definem **replication boundaries** (sincronização de dados entre domain controllers)
- Estabelecem **authentication and authorization boundaries** (limites de acesso a recursos)

#### Árvores (Trees)
Uma **árvore de domínios** é uma hierarquia de domínios dentro de um AD DS.

**Características:**
- Todos os domínios compartilham um **namespace contíguo** com o domínio pai
- Podem conter domínios filhos adicionais
- Criam automaticamente **two-way transitive trusts** com outros domínios na árvore

#### Unidades Organizacionais (OUs)
As **OUs** são contêineres dentro de um domínio usados para organizar e gerenciar objetos.

**Características:**
- Representam a estrutura organizacional de forma **hierárquica e lógica**
- Simplificam a **delegação de tarefas administrativas** com controle granular de permissões
- Permitem aplicar **Objetos de Política de Grupo (GPOs)** para gestão centralizada
- Podem conter usuários, grupos, computadores e outras OUs

---

### Componentes Físicos do Active Directory

#### Domain Controllers (DCs)

Um **Domain Controller** é um servidor com o role **Active Directory Domain Services (AD DS)** instalado e promovido especificamente para esta função.

**Conceitos:**
- **Role:** Grupo de programas que permite ao computador exercer uma função específica na rede (FTP, DNS, DHCP, etc.)
- **Role Services:** Programas que fornecem funcionalidade para um role específico (Resource Manager, etc.)
- **Characteristics:** Programas que complementam ou aumentam funcionalidade (load balancing, clustering, SNMP, etc.)

**Responsabilidades do Domain Controller:**
- Hospeda uma cópia do **diretório AD DS** (objetos: computadores, usuários, etc.)
- Fornece serviços de **autenticação e autorização** via **Kerberos**
- **Replica atualizações** para outros DCs no domínio e floresta
- Permite acesso administrativo para gerenciar contas e recursos de rede
- Gerencia a estrutura **LDAP** do domínio e mantém uma cópia do banco de dados **NTDS.dit**

**Implementação:**
- Um DC pode ser **primário ou secundário**, com cópias para **load balancing** e **alta disponibilidade**
- **Nunca** deve ser exposto à internet, mas precisa de acesso à internet para atualizações e instalação de roles — sempre protegido por firewall robusto
- Desde **Windows Server 2016**, um DC suporta **interoperabilidade** entre infraestrutura on-premise e cloud (Azure, Office 365)
- É o **alvo prioritário** de Red Teams e pentesters — sua comprometimento significa compromisso de toda a rede corporativa

#### Configuração de Rede

Um Domain Controller **deve ter IP estático** para que todos os computadores o localizem sempre. Geralmente atua como **DNS** também, então o IP estático é o mesmo do servidor DNS.

#### AD DS Data Store

O **AD DS Data Store** contém os arquivos de banco de dados e processos que armazenam e gerenciam informações de diretório para usuários, serviços e aplicações. **Também contém hashes de senhas.**

**Características:**
- Consiste no arquivo **NTDS.dit**
- Armazenado por padrão em `%SystemRoot%\NTDS` em todos os domain controllers
- Acessível apenas através de processos e protocolos do domain controller

---

### Componentes Lógicos do Active Directory

#### Schema do AD DS

O **Schema do AD DS** define a estrutura hierárquica que armazena informações sobre redes e domínios em um banco de dados. É projetado para ambientes de rede distribuída.

**Características:**
- Utiliza protocolos como **LDAP, DNS, DHCP** e outros
- Define que **tipos de objetos** serão armazenados
- Aplica **segurança, autorização e autenticação** baseadas em regras e permissões definidas no nível do DC

**Tipos de Objetos:**

| Tipo de Objeto | Função | Exemplo |
|:---------------|:-------|:--------|
| **Class Object** | Define que objetos podem ser criados no diretório | User, Computer, Group |
| **Attribute Object** | Informações que podem ser anexadas a um objeto | Display Name, Domain, Email |

#### Objects (Objetos)

Tudo que é criado em um domínio AD é armazenado como um **objeto**. Os principais tipos são:

| Objeto | Descrição |
|:-------|:----------|
| **User** | Habilita acesso a recursos de rede para um usuário |
| **InetOrgPerson** | Similar a conta de usuário; usado para compatibilidade com outros serviços de diretório |
| **Contacts** | Usado principalmente para atribuir endereços de email a usuários externos; não habilita acesso à rede |
| **Computers** | Habilita autenticação e auditoria do acesso de computadores a recursos |
| **Printers** | Simplifica o processo de localizar e conectar a impressoras |
| **Shared Folders** | Permite aos usuários buscar compartilhamentos com base em propriedades |
| **Groups** | Simplifica a administração de controle de acesso |

#### Grupos

Um **grupo** é um conjunto de contas de usuário, computadores, contatos e outros grupos que podem ser gerenciados como uma unidade. Os membros de um grupo herdam as permissões atribuídas a ele.

**Usos:**
- Simplificar gerenciamento de permissões (atribuir permissão a um grupo = todos os membros têm a permissão)
- Delegar administração (atribuir direitos a grupos)
- Criar listas de distribuição de email

**Escopos de Grupos:**

| Escopo | Características |
|:-------|:----------------|
| **Local Domain** | Permissões apenas dentro de um domínio. Pode conter global groups (qualquer domínio), universal groups (qualquer domínio), local domain groups (mesmo domínio). |
| **Global Domain** | Permissões apenas dentro da mesma floresta. |
| **Universal** | Permissões em toda a floresta. |

**Tipos:**
- **Security Groups:** Usados para permissões de recursos compartilhados
- **Distribution Groups:** Usados para listas de distribuição de email

#### Trusts (Confianças)

**Trusts** fornecem um mecanismo para usuários de um domínio acessarem recursos em outro domínio.

**Tipos de Trusts:**

| Tipo | Descrição |
|:-----|:----------|
| **Directional (Direcional)** | O fluxo de confiança é do domínio confiante para o domínio confiável. Unidirecional. |
| **Transitive (Transitivo)** | A relação de confiança se estende além de dois domínios para incluir outros domínios confiáveis. Todos os domínios em uma floresta confiam em todos os outros domínios da floresta. |

**Características:**
- Trusts entre domínios na mesma floresta são **automáticas e transitivas**
- Trusts entre florestas devem ser **explicitamente criados**
- Trusts podem se estender **fora da floresta**

#### Group Policy Objects (GPOs)

**GPOs** são a forma mais simples de configurar opções de usuários e computadores em redes AD.

**Características:**
- Complementam as **OUs** — a estrutura deve ser bem projetada
- Vinculados a **OUs** contendo grupos, que contêm usuários e computadores
- Permitem configuração centralizada de políticas de segurança, software e configurações do sistema

---

### Resumo da Estrutura

---

## Kerberos

*[Seção a ser desenvolvida]*

---

## Segurança e Exploração

Um ambiente Active Directory pode ser explorado **sem exploits corrigíveis**, abusando de suas funcionalidades inerentes, como:

- **Domain Trusts** (confianças entre domínios)
- **Delegação de autenticação** (constrained/unconstrained delegation)
- **Grupos privilegiados** e herança de permissões
- **Políticas de grupo** (Group Policy Objects)
- **Replicação de dados** entre domain controllers

*[Seção a ser desenvolvida com técnicas específicas]*



## Documentação

- [Visão geral dos Serviços de Domínio do Active Directory](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)