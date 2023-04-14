## Java Platform Standard Edition 11 Development Kit - JDK 11

### Introduction
**소개**

Thank you for downloading this release of the Java Platform, Standard Edition Development Kit (JDK). 
The JDK is a development environment for building applications and components using the Java programming language.

The JDK includes tools useful for developing, testing, and monitoring programs written in the Java programming language and running on the Java platform.
> 11 버전의 JDK를 설치해주셔서 감사합니다. <br>
> JDK는 자바로 애플리케이션과 컴포넌트를 만들기 위한 개발환경입니다. <br>
> JDK는 자바로 만들어지고, 자바 플랫폼에서 실행되는 프로그램의 개발, 테스트, 모니터링을 위한 유용한 도구들을 제공합니다. 

<br>

### Installation
**설치**

Installation instructions are located on the Java SE documentation site.
> 설치 방법은 Java SE 설명 문서에 있습니다.

<br>

### JDK Documentation
**JDK 설명 문서**

The on-line Java Platform, Standard Edition (Java SE) Documentation contains API specifications, feature descriptions, 
developer guides, reference pages for JDK tools and utilities, and links to related information. <br>
The Java SE documentation is also available in a download bundle which you can install on your machine. <br>
To obtain the documentation bundle visit the Java SE download page. <br>
For API documentation, refer to the The Java Platform, Standard Edition API Specification. <br>
This documentation provides brief descriptions of the API with an emphasis on specifications, not on code examples.


> 온라인 자바 SE 설명서에는 API 명세서, 기능 설명, 개발자 안내서, JDK 도구 및 유틸리티에 대한
> 참조 페이지, 관련 정보에 대한 링크가 포함되어 있습니다. <br>
> 자바 SE 설명서는 당신의 기계에 직접 설치할 수 있는 다운로드 번들로도 제공합니다. <br>
> `Java SE download page` 에서 번들을 설치할 수 있습니다. <br>
> API 설명서는 `Java Platform, Standard Edition API Specification` 을 참조하십시오. <br>
> 이 설명서는 코드 예제 없이, API 명세에 중점을 둔 간략한 설명을 제공합니다.

<br>

### Release Notes
**변경 사항**

See the Release Notes for additional information pertaining to this release. <br>
Please check the on-line release notes for the latest information as they will be updated as needed.

> 변경 사항의 자세한 정보를 알고 싶다면,  `Release Notes` 를 참조하십시오. <br>
> 최신 변경 사항은 필요에 따라 업데이트 되므로, `on-line release notes` 를 확인하십시오.

<br>

### Compatibility
**호환성**

See the JDK 11 Migration Guide for a list of known compatibility issues. <br>
Every effort has been made to support programs written for previous versions of the Java platform. <br>
Although some incompatible changes were necessary, most software should migrate to the current version with no changes. <br>
Most failures to do so are considered bugs, except for a small number of cases where compatibility was deliberately broken, as 
described on our compatibility web page. <br>
Some compatibility-breaking changes were required to close potential security holes or to fix implementation or design bugs. <br>

> 잘 알려진 호환성 관련 문제들은 `JDK 11 Migration Guide` 를 참고하세요. <br>
> 이전 버전의 자바 플랫폼을 사용한 프로그램들을 지원하기 위해 많은 노력을 기울였습니다. <br>
> 비록 일정 부분은 호환되지 않아 변경이 필요하지만, 대부분의 프로그램들은 변경 없이 현재 버전으로 변경해야 합니다. <br>
> 변경하지 않을 경우 (의도적으로 호환성을 깨뜨린 소수의 경우를 제외하고는) 이는 버그로 간주됩니다. <br>
> 잠재적인 보안 허점이나 구현 및 설계상의 버그를 막기 위해, 일부분은 이전 버전과의 호환성을 깨는 변경이 필요했습니다. <br>

<br>

### Bug Reports and Feedback
**버그 신고 및 피드백**

The JDK Bug Database web site lets you search for and examine existing bug reports, submit your own bug reports, and tell us which bug fixes
matter most to you. <br>
To directly submit a bug or request a feature, fill out this form : <br>
<br>
http://bugreport.java.com/bugreport/
<br>
You can send feedback to the Java SE documentation team. <br>
<br>
Please do not seek technical support through the Bug Database or our development teams. <br>
For support options, see Support and Services on Oracle Support web site.

> `JDK Bug Database` 웹 사이트를 사용하여 버그 보고서를 검색하고, 당신이 발견한 버그를 신고하고, 버그가 고쳐졌음을 저희에게 알릴 수 있습니다. <br>
> 버그 신고를 하려면 다음 양식을 제출하십시오 <br> <br>
> http://bugreport.java.com/bugreport/ <br><br>
> 당신은 `Java SE documentation team` 에 피드백을 보낼 수 있습니다. <br>
> <br>
> `Bug database` 팀이나 개발팀에게 기술적 지원을 요청하지 마십시오. <br>
> 지원 요청은, `Oracle Support web site` 의 `Support and Services` 를 참조하십시오.

<br>

### Contents of the JDK
**JDK 구성 요소**

This section contains a general summary of the files and directories in the JDK. <br>




> 이 곳에서 JDK의 파일들과 디렉토리들을 요약합니다. <br>

**Executables**
> **실행 파일** <br>

(In the bin/ subdirectory) An implementation of the Java Runtime Environment (JRE). <br>
The JRE includes a Java Virtual Machine (JVM™), class libraries, and other files that support the execution of programs
written in the Java programming language. <br>
This directory also includes tools and utilities that will help you develop, execute, 
debug, and document programs written in the Java programming language.<br>
For further information, see the tools documentation at https://docs.oracle.com/javase/11/tools <br>

> (bin/ 하위 디렉토리) JRE의 구현입니다. <br>
> JRE는 JVM, 클래스 라이브러리, 자바로 작성된 프로그램 실행을 돕는 다양한 파일들로 이루어져 있습니다. <br>
> 또한 이 디렉토리는 개발, 실행, 디버깅, 문서 작성을 돕는 다양한 툴들이 포함됩니다. <br>
> 더 많은 정보가 필요하다면, `https://docs.oracle.com/javase/11/tools` 의 `tools` 를 확인하십시오.

**Configuration files**

(In the conf/ subdirectory) Files that contain user-configurable options. <br>
Files in this directory can be edited to change the JDK's access permissions, configure security algorithms, 
and set the Java Cryptography Extension Policy Files which might be used to limit the JDK's cryptographic strength.<br>

> **설정 파일** <br>
> (conf/ 하위 디렉토리) 사용자가 변경할 수 있는 옵션들을 파일로 갖고 있습니다. <br>
> 이 디렉토리의 파일들을 편집하여,  JDK의 접근 권한을 변경하고, 보안 알고리즘을 설정하고, 
> JDK의 암호화 강도를 제한하는 데 필요한 `Java Cryptography Extension Policy Files` 을 설정할 수 있습니다.

**C header Files**
(In the include/ subdirectory) C-language header files that support native-code programming with the Java Native 
Interface and the Java Virtual Machine (JVM) Debugger Interface.
> **C 헤더 파일** <br>
> (include/ 하위 디렉토리) `Java Native Interface` 와 ` JVM Debugger Interface` 를 사용한 네이티브 코드 프로그래밍을 
> 지원하는 C 언어 헤더파일입니다. 

**Compiled Java Modules**
(in the jmods/ subdirectory) Compiled modules used by jlink to create custom runtimes.
>**컴파일된 자바 모듈** <br>
> (jmods/ 하위 디렉토리) 사용자 지정 런타임을 만드는 `jlink` 를 사용한 컴파일 모듈입니다.

**Copyright and License files**
(in the legal/ subdirectory) License and copyright files for each module. <br>
Includes third party notices as .md (markdown)files.
>**저작권 및 라이센스 파일**<br>
> (legal/ 하위 디렉토리) 각 모듈별로 필요한 라이센스와 저작권 파일입니다. <br>
> 서드 파티의 경우 마크다운파일로 표시했습니다.

**Additional Libraries**
(In the lib/ subdirectory) Additional class libraries and support files required by the JDK. <br>
These files are not intended for external use.

> **추가 라이브러리**<br>
> (lib/ 하위 디렉토리) JDK 가 필요로하는 추가적인 클래스 라이브러리와 지원 파일 입니다. <br>
> 이 파일들은 외부에서 사용하는 용도는 아닙니다.

<br>

### Configuration
**설정**

Configuring the JRE or JDK is not considered a modification for redistribution purposes. <br>

You may configure the software by modifying the files under the conf/ directory as per the 
Java Platform, Standard Edition (Java SE) Documentation, including selecting one or creating 
your own Java Cryptography Extension policy file under conf/security/policy. <br>
Once you select or create your own you are not required to include alternative policy files. <br>
Root CA certificates may be added to or removed from the Java SE certificate file located in 
lib/security/cacerts through the use of the keytool utility available in the bin/ subdirectory of the JDK. <br>

You may update the Timezone data included in the Java Runtime Environment by using the Java Time Zone Updater tool 
available in the Java SE Downloads page. <br>

> JRE 와 JDK 는 구성은 재배포 목적으로 수정된 것으로 간주되지 않습니다. <br>
> 당신은 conf/ 디렉토리 하위의 파일들을 수정하여 소프트웨어를 구성할 수 있습니다. <br>
> 당신이 직접 선택하거나 만든 경우에는 대체 정책 파일을 포함할 필요가 없습니다. <br>
> 루트 CA 인증서는 keytool 유틸리티를 사용하여 Java SE 인증서 파일에서 추가하거나 제거할 수 있습니다. <br>
> 
> Java SE 다운로드 페이지에 있는 Java Time Zone Updater tool 을 사용하여 JRE 의 Timezone 을 수정할 수 있습니다.

<br>

### Unlimited Strength Java Cryptography Extension
**무제한 강도의 Java 암호화 확장**

The default JCE policy files bundled in this Java Runtime Environment allow for "unlimited" cryptographic strengths. <br>

For convenience, this software also contains the historic "limited" strength policy files which restricts cryptographic strengths. <br>
To use the limited strength policy, instead of the default unlimited policy, you must update the "crypto.policy" 
Security property (in /conf/security/java.security) to point to the appropriate directory. <br>

You are advised to consult your export/import control counsel or attorney to determine the exact requirements of your location, 
and what policy settings should be used.

> JRE의 번들로 제공되는 기본 JCE 정책 파일은 "무제한" 암호화 강도를 지원합니다. <br>
> 
> 편의를 위해, 암호화 강도를 제한하는 과거에 사용된 "제한된" 암호화 강도 정책 파일도 포함됩니다. <br>
> 기본으로 제공되는 무제한 강도 정책 대신, 제한된 강도 정책을 사용하기 위해선, "crypto.policy" 보안 속성이 적절한 디렉토리를
> 가리키도록 수정해야 합니다. <br>
> 
> 당신이 사는 지역에서 정확한 요구사항과 어떤 정책이 필요한 지 결정하려면, 당신의 수출입 관리 통제사 또는 변호사와 상의가 필요합니다.