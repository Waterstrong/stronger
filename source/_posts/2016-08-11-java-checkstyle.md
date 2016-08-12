---
title: Java Checkstyle in Gradle
date: 2016-08-11 22:31:37
category: Tools
tags: [Java, Checkstyle]
description: Checkstyle是一个帮助Java开发者遵守某些编码规范的工具，它能够自动化代码规范检查过程，从而使得开发者从这项重要但枯燥的任务中解脱出来，Checkstyle通常适合那些需要强制执行编码规范标准的项目。
published: true
---

Checkstyle是一个帮助Java开发者遵守某些编码规范的工具，它能够自动化代码规范检查过程，从而使得开发者从这项重要但枯燥的任务中解脱出来，Checkstyle通常适合那些需要强制执行编码规范标准的项目。

> Checkstyle is a development tool to help programmers write Java code that adheres to a coding standard. It automates the process of checking Java code to spare humans of this boring (but important) task. This makes it ideal for projects that want to enforce a coding standard.

通常的检查项目包括Javadoc注释、命名约定、Title标题、Import语句、文件大小、空白、修饰符、代码块、类设计、混合检查等。可以阅读[Checkstyle Checks](http://checkstyle.sourceforge.net/checks.html)了解更多。

在Gradle中，实现对Java的编码规范检查，只需要三步即可：
* 1) 在`build.gradle`中引入Checkstyle插件`apply plugin: 'checkstyle'`。
* 2) 在项目目录下添加默认的文件夹`config/checkstyle`，然后新建默认配置文件[checkstyle.xml](/assets/java-checkstyle/checkstyle.xml)文件，并配置编码规范检查规则。比如这里给出一个编码规范配置的示例：
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE module PUBLIC "-//Puppy Crawl//DTD Check Configuration 1.3//EN" "http://www.puppycrawl.com/dtds/configuration_1_3.dtd">

<module name="Checker">
    <module name="SuppressionFilter">
        <property name="file" value="config/checkstyle/suppressions.xml"/>
    </module>

    <!-- Checks that property files contain the same keys. -->
    <module name="Translation" />

    <!-- All text file should end with a blank line. -->
    <module name="NewlineAtEndOfFile">
        <property name="lineSeparator" value="lf" />
    </module>

    <!-- The maximum number of lines allow in any source file is 200 -->
    <module name="FileLength" >
        <property name="max" value="200"/>
    </module>

    <!-- Tab characters are not allowed in source files. -->
    <module name="FileTabCharacter" />

    <module name="RegexpSingleline">
        <!-- Checks that FIXME is not used in comments.  TODO is preferred.
        -->
        <property name="format" value="((//.*)|(\*.*))FIXME" />
        <property name="message" value='TODO is preferred to FIXME.  e.g. "TODO(johndoe): Refactor when v2 is released."' />
    </module>

    <module name="RegexpSingleline">
        <!-- Checks that TODOs are named.  (Actually, just that they are followed
             by an open paren.)
        -->
        <property name="format" value="((//.*)|(\*.*))TODO[^(]" />
        <property name="message" value='All TODOs should be named.  e.g. "TODO(johndoe): Refactor when v2 is released."' />
    </module>

    <module name="TreeWalker">
        <module name="FileContentsHolder" />

        <property name="cacheFile" value="checkstyle.cache" />

        <property name="tabWidth" value="4" />

        <!-- only check java files. -->
        <property name="fileExtensions" value="java" />

        <!-- Checks for Javadoc comments.                     -->
        <!-- See http://checkstyle.sf.net/config_javadoc.html -->

        <module name="JavadocMethod">
            <property name="scope" value="protected"/>
            <property name="severity" value="warning"/>
            <property name="allowMissingJavadoc" value="true"/>
            <property name="allowMissingParamTags" value="true"/>
            <property name="allowMissingReturnTag" value="true"/>
            <property name="allowMissingThrowsTags" value="true"/>
            <property name="allowThrowsTagsForSubclasses" value="true"/>
            <property name="allowUndeclaredRTE" value="true"/>
        </module>

        <!--
        <module name="JavadocType">
            <property name="scope" value="protected"/>
            <property name="severity" value="error"/>
        </module>
        -->

        <module name="JavadocStyle">
            <property name="severity" value="warning"/>
        </module>


        <!-- Naming Conventions -->
        <module name="ConstantNameCheck">
            <!-- Validates non-private, static, final fields against the supplied
            public/package final fields "^[A-Z][A-Z0-9]*(_[A-Z0-9]+)*$". -->
            <metadata name="altname" value="ConstantName"/>
            <property name="applyToPublic" value="true"/>
            <property name="applyToProtected" value="true"/>
            <property name="applyToPackage" value="true"/>
            <property name="applyToPrivate" value="false"/>
            <property name="format" value="^([A-Z][A-Z0-9]*(_[A-Z0-9]+)*|FLAG_.*)$"/>
            <message key="name.invalidPattern"
                     value="Variable ''{0}'' should be in ALL_CAPS (if it is a constant) or be private (otherwise)."/>
            <property name="severity" value="error"/>
        </module>

        <module name="StaticVariableNameCheck">
            <!-- Validates static, non-final fields against the supplied
            expression "^[a-z][a-zA-Z0-9]*_?$". -->
            <metadata name="altname" value="StaticVariableName"/>
            <property name="applyToPublic" value="true"/>
            <property name="applyToProtected" value="true"/>
            <property name="applyToPackage" value="true"/>
            <property name="applyToPrivate" value="true"/>
            <property name="format" value="^[a-z][a-zA-Z0-9]*_?$"/>
            <property name="severity" value="error"/>
        </module>

        <module name="MemberNameCheck">
            <!-- Validates non-static members against the supplied expression. -->
            <metadata name="altname" value="MemberName"/>
            <property name="applyToPublic" value="true"/>
            <property name="applyToProtected" value="true"/>
            <property name="applyToPackage" value="true"/>
            <property name="applyToPrivate" value="true"/>
            <property name="format" value="^[a-z][a-zA-Z0-9]*$"/>
            <property name="severity" value="error"/>
        </module>

        <module name="MethodNameCheck">
            <!-- Validates identifiers for method names. -->
            <metadata name="altname" value="MethodName"/>
            <property name="format" value="^[a-z][a-zA-Z0-9]*(_[a-zA-Z0-9]+)*$"/>
            <property name="severity" value="error"/>
        </module>

        <module name="PackageName">
            <property name="format" value="^[a-z]+(\.[a-z][a-z0-9]*)*$" />
        </module>

        <module name="LocalFinalVariableName">
            <!-- Validates identifiers for local final variables against the
              expression "^[a-z][a-zA-Z0-9]*$". -->
            <property name="severity" value="warning"/>
        </module>

        <module name="LocalVariableName">
            <!-- Validates identifiers for local variables against the
              expression "^[a-z][a-zA-Z0-9]*$". -->
            <property name="severity" value="warning"/>
        </module>

        <module name="TypeNameCheck">
            <!-- Validates static, final fields against the
            expression "^[A-Z][a-zA-Z0-9]*$". -->
            <metadata name="altname" value="TypeName"/>
            <property name="severity" value="warning"/>
        </module>

        <!-- Import check -->
        <module name="AvoidStarImport" />
        <module name="IllegalImport" />
        <module name="RedundantImport">
            <!-- Checks for redundant import statements. -->
            <property name="severity" value="error"/>
        </module>
        <module name="UnusedImports"/>
        <module name="ImportOrder">
            <!-- Checks for out of order import statements. -->
            <property name="severity" value="error"/>
            <property name="groups" value="java,javax,org,net,com"/>
            <!-- This ensures that static imports go first. -->
            <property name="option" value="top"/>
            <property name="tokens" value="STATIC_IMPORT, IMPORT"/>
        </module>

        <!-- Length check -->
        <module name="LineLength">
            <!-- Checks if a line is too long. -->
            <property name="max" value="${com.puppycrawl.tools.checkstyle.checks.sizes.LineLength.max}" default="112"/>
            <property name="severity" value="error"/>

            <!--
              The default ignore pattern exempts the following elements:
                - import statements
                - long URLs inside comments
            -->

            <property name="ignorePattern"
                      value="${com.puppycrawl.tools.checkstyle.checks.sizes.LineLength.ignorePattern}"
                      default="^(package .*;\s*)|(import .*;\s*)|( *\* *https?://.*)$"/>
        </module>
        <module name="MethodLength">
            <property name="tokens" value="METHOD_DEF"/>
            <property name="max" value="20"/>
            <property name="countEmpty" value="false"/>
            <property name="severity" value="warning" />
        </module>
        <module name="ParameterNumber">
            <property name="max" value="5"/>
            <property name="tokens" value="METHOD_DEF"/>
        </module>

        <module name="LeftCurly">
            <!--<property name="severity" value="error"/>-->
            <!--<property name="option" value="nl"/>-->
            <!--<property name="tokens" value="CLASS_DEF,INTERFACE_DEF, METHOD_DEF"/>-->
        </module>

        <module name="RightCurly">
            <!-- Checks right curlies on CATCH, ELSE, and TRY blocks are on
            the same line. e.g., the following example is fine:
            <pre>
              if {
                ...
              } else
            </pre>
            -->
            <!-- This next example is not fine:
            <pre>
              if {
                ...
              }
              else
            </pre>
            -->
            <property name="option" value="same"/>
            <property name="severity" value="warning"/>
        </module>

        <!-- Checks for braces around if and else blocks -->
        <module name="NeedBraces">
            <property name="severity" value="warning"/>
            <property name="tokens" value="LITERAL_IF, LITERAL_ELSE, LITERAL_FOR, LITERAL_WHILE, LITERAL_DO"/>
        </module>

        <module name="UpperEll">
            <!-- Checks that long constants are defined with an upper ell.-->
            <property name="severity" value="error"/>
        </module>


        <!-- Whites Space check -->
        <module name="EmptyForInitializerPad">
            <property name="severity" value="warning"/>
        </module>
        <module name="NoWhitespaceAfter">
            <property name="tokens" value="BNOT, DEC, DOT, INC, LNOT,UNARY_MINUS, UNARY_PLUS"/>
            <property name="allowLineBreaks" value="false"/>
            <property name="severity" value="warning"/>
        </module>

        <module name="FallThrough">
            <!-- Warn about falling through to the next case statement.  Similar to
            javac -Xlint:fallthrough, but the check is suppressed if a single-line comment
            on the last non-blank line preceding the fallen-into case contains 'fall through' (or
            some other variants which we don't publicized to promote consistency).
            -->
            <property name="reliefPattern"
                      value="fall through|Fall through|fallthru|Fallthru|falls through|Falls through|fallthrough|Fallthrough|No break|NO break|no break|continue on"/>
            <property name="severity" value="error"/>
        </module>


        <!-- Line Wrap -->
        <module name="OperatorWrap">
            <property name="option" value="eol"/>
            <property name="tokens"
                      value="BAND, BOR, BSR, BXOR, DIV, EQUAL, GE, GT, LAND, LE, LITERAL_INSTANCEOF, LOR, LT, MINUS, MOD, NOT_EQUAL, QUESTION, SL, SR, STAR" />
        </module>
        <module name="OperatorWrap">
            <property name="option" value="nl"/>
            <property name="tokens" value="COLON"/>
        </module>

        <module name="ParenPad" />
        <module name="TypecastParenPad" />
        <module name="WhitespaceAfter">
            <!-- Checks that commas, semicolons and typecasts are followed by
                 whitespace.
            -->
            <property name="tokens" value="COMMA, SEMI, TYPECAST"/>
        </module>
        <module name="WhitespaceAround">
            <property name="tokens"
                      value="ASSIGN, BAND, BAND_ASSIGN, BOR, BOR_ASSIGN, BSR, BSR_ASSIGN, BXOR, BXOR_ASSIGN, COLON, DIV, DIV_ASSIGN, EQUAL, GE, GT, LAND, LCURLY, LE, LITERAL_ASSERT, LITERAL_CATCH, LITERAL_DO,  LITERAL_ELSE, LITERAL_FINALLY, LITERAL_FOR, LITERAL_IF, LITERAL_RETURN, LITERAL_SYNCHRONIZED, LITERAL_TRY, LITERAL_WHILE, LOR, LT, MINUS, MINUS_ASSIGN, MOD, MOD_ASSIGN, NOT_EQUAL, PLUS, PLUS_ASSIGN, QUESTION, RCURLY, SL, SLIST, SL_ASSIGN, SR, SR_ASSIGN, STAR, STAR_ASSIGN, TYPE_EXTENSION_AND"/>
        </module>

        <module name="ModifierOrder">
            <!-- Warn if modifier order is inconsistent with JLS3 8.1.1, 8.3.1, and
                 8.4.3.  The prescribed order is:
                 public, protected, private, abstract, static, final, transient, volatile,
                 synchronized, native, strictfp
              -->
        </module>
        <module name="RedundantModifier"/>
        <module name="EmptyBlock" />
        <module name="NeedBraces" />
        <module name="RightCurly" />
        <module name="AvoidNestedBlocks" />

        <module name="CovariantEquals" />
        <module name="EmptyStatement" />
        <module name="EqualsHashCode" />
        <module name="IllegalInstantiation">
            <property name="classes" value="java.lang.Boolean"/>
        </module>
        <module name="InnerAssignment" />
        <module name="MissingSwitchDefault" />
        <module name="RedundantThrows" />
        <module name="SimplifyBooleanReturn" />
        <module name="StringLiteralEquality" />
        <module name="NestedIfDepth" >
            <property name="max" value="1"/>
        </module>
        <module name="NestedTryDepth">
            <property name="max" value="1"/>
        </module>
        <module name="SuperClone"/>
        <module name="SuperFinalize" />
        <module name="PackageDeclaration" />
        <module name="JUnitTestCase" />
        <module name="ReturnCount" >
            <property name="max" value="2"/>
        </module>
        <module name="IllegalType"/>
        <module name="DeclarationOrder" />
        <module name="ParameterAssignment" />
        <module name="VisibilityModifier">
            <property name="protectedAllowed" value="true" />
        </module>
        <module name="FinalClass" />
        <module name="InterfaceIsType" >
            <property name="allowMarkerInterfaces" value="true"/>
        </module>
        <!--<module name="HideUtilityClassConstructor" />-->
        <module name="MutableException" />
        <module name="ThrowsCount">
            <property name="max" value="3"/>
        </module>
        <module name="CyclomaticComplexity">
            <property name="max" value="7"/>
        </module>
        <module name="UpperEll"/>
        <module name="ArrayTypeStyle" />
    </module>

    <module name="SuppressionCommentFilter">
        <property name="offCommentFormat" value="CHECKSTYLE\:OFF ([\w\|]+)" />
        <property name="onCommentFormat" value="CHECKSTYLE\:ON ([\w\|]+)" />
        <property name="checkFormat" value="$1" />
    </module>

</module>
```

当然，除了使用默认文件名外，也可以在`build.gradle`中自定义规范检查的配置文件，并且可以针对Main和Test类代码的编码规范分别配置，因为通常对Test类代码的编码规范检查并没有那么严格。
```
todo
```

* 3) 在同一目录下新建默认忽略检查规范的文件[suppressions.xml](/assets/java-checkstyle/suppressions.xml)，可以配置忽略对某个文件的某个规则的检查。比如：
```
<?xml version="1.0"?>

<!DOCTYPE suppressions PUBLIC
        "-//Puppy Crawl//DTD Suppressions 1.1//EN"
        "http://www.puppycrawl.com/dtds/suppressions_1_1.dtd">

<suppressions>
    <suppress files="SimpleCORSFilter" checks="RedundantThrows" />
</suppressions>
```

该`suppressions.xml`需要在`checkstyle.xml`文件或`build.gradle`中配置，默认执行check会触发编码规范检查`checkstyleMain`和`checkstyleTest`。

OK，配置完成后可以放心写代码了，再也不用担心团队成员编码规范不统一的问题了，Enjoy~


----
References

* [Sourceforge Checkstyle](http://checkstyle.sourceforge.net/)
* [Google Java Style](http://checkstyle.sourceforge.net/reports/google-java-style.html)
* [The Checkstyle Plugin](https://docs.gradle.org/current/userguide/checkstyle_plugin.html)