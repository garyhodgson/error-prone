---
title: InjectScopeOrQualifierAnnotationRetention
summary: Scoping and qualifier annotations must have runtime retention.
layout: bugpattern
category: INJECT
severity: ERROR
maturity: EXPERIMENTAL
---

<!--
*** AUTO-GENERATED, DO NOT MODIFY ***
To make changes, edit the @BugPattern annotation or the explanation in docs/bugpattern.
-->

## The problem
The JSR-330 spec allows use of reflection. Not having runtime retention on scoping or qualifer annotations will cause unexpected behavior in frameworks that use reflection.

## Suppression
Suppress false positives by adding an `@SuppressWarnings("InjectScopeOrQualifierAnnotationRetention")` annotation to the enclosing element.

----------

## Examples
__InjectScopeOrQualifierAnnotationRetentionNegativeCases.java__

{% highlight java %}
/*
 * Copyright 2013 Google Inc. All Rights Reserved.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *     http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

package com.google.errorprone.bugpatterns;

import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.ElementType.TYPE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;
import static java.lang.annotation.RetentionPolicy.SOURCE;

import com.google.inject.BindingAnnotation;
import com.google.inject.ScopeAnnotation;

import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import javax.inject.Qualifier;
import javax.inject.Scope;

/**
 * @author sgoldfeder@google.com (Steven Goldfeder)
 */
public class InjectScopeOrQualifierAnnotationRetentionNegativeCases {
  /**
   * A scoping (@Scope) annotation with runtime retention
   */
  @Scope
  @Target({TYPE, METHOD})
  @Retention(RUNTIME)
  public @interface TestAnnotation1 {
  }

  /**
   * A scoping (@ScopingAnnotation) annotation with runtime retention.
   */
  @ScopeAnnotation
  @Target({TYPE, METHOD})
  @Retention(RUNTIME)
  public @interface TestAnnotation2 {
  }

  /**
   * A qualifer(@Qualifier) annotation with runtime retention.
   */
  @Qualifier
  @Target({TYPE, METHOD})
  @Retention(RUNTIME)
  public @interface TestAnnotation3 {
  }

  /**
   * A qualifer(@BindingAnnotation) annotation with runtime retention.
   */
  @BindingAnnotation
  @Target({TYPE, METHOD})
  @Retention(RUNTIME)
  public @interface TestAnnotation4 {
  }

  /**
   * A non-qualifer, non-scoping annotation without runtime retention.
   */
  @Retention(SOURCE)
  public @interface TestAnnotation5 {
  }
}
{% endhighlight %}

__InjectScopeOrQualifierAnnotationRetentionPositiveCases.java__

{% highlight java %}
/*
 * Copyright 2013 Google Inc. All Rights Reserved.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *     http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

package com.google.errorprone.bugpatterns;

import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.ElementType.TYPE;
import static java.lang.annotation.RetentionPolicy.SOURCE;

import com.google.inject.BindingAnnotation;
import com.google.inject.ScopeAnnotation;

import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import javax.inject.Qualifier;
import javax.inject.Scope;

/**
 * @author sgoldfeder@google.com (Steven Goldfeder)
 */
public class InjectScopeOrQualifierAnnotationRetentionPositiveCases {
  /**
   * A scoping (@Scope) annotation with SOURCE retention
   */
  @Scope
  @Target({TYPE, METHOD})
  // BUG: Diagnostic contains: @Retention(RUNTIME) 
  @Retention(SOURCE)
  public @interface TestAnnotation1 {
  }

  /**
   * A scoping (@ScopingAnnotation) annotation with SOURCE retention.
   */
  @ScopeAnnotation
  @Target({TYPE, METHOD})
  // BUG: Diagnostic contains: @Retention(RUNTIME) 
  @Retention(SOURCE)
  public @interface TestAnnotation2 {
  }

  /**
   * A qualifer(@Qualifier) annotation with SOURCE retention.
   */
  @Qualifier
  @Target({TYPE, METHOD})
  // BUG: Diagnostic contains: @Retention(RUNTIME) 
  @Retention(SOURCE)
  public @interface TestAnnotation3 {
  }

  /**
   * A qualifer(@BindingAnnotation) annotation with SOURCE retention.
   */
  @BindingAnnotation
  @Target({TYPE, METHOD})
  // BUG: Diagnostic contains: @Retention(RUNTIME) 
  @Retention(SOURCE)
  public @interface TestAnnotation4 {
  }
  
  /**
   * A qualifer annotation with default retention.
   */
  @BindingAnnotation
  @Target({TYPE, METHOD})
  // BUG: Diagnostic contains: @Retention(RUNTIME) 
  public @interface TestAnnotation5 {
  }
}
{% endhighlight %}
