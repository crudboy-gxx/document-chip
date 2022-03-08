【2017/8/28】
1.Bean Validation有两个规范:BeanValidation1.0(即JSR303)和BeanValidation1.1(即 JSR349)。
	1）主要用于对数据进行校验,确保输入进来的数据从语义上来讲是正确的。
	2）特点：JSR 303 用于对Java Bean 中的字段的值进行验证,使得验证逻辑从业务代码中脱离出来。是一个运行时的数据验证框架，在验证之后验证的错误信息会被马上返回。
	3）应用场景:一般用于表单提交页面(如用户名必填、只能由数字字母组成等等)。
	
2.Bean Validation 1.1当前实现是Hibernate validator 5，且spring4才支持

3.验证可以使用注解验证或者代码验证。注解验证不通过抛出异常，页面返回不符合规范数据的提示（无法返回之前的form页面）；代码可以返回页面，并提示问题数据。

4.验证的时机：提交的表单！service返回的结果？dao返回的结果？

5.@Validated spring-validation 和 @Valid habernate-validation

【2017/8/29】
1.beanValidation提供bean校验规范接口，使用当期流行版本1.1（2.0），habernateValidation是官方实现。

2.自定义注解参考@Email注解，通过@Constraint(validatedBy = { })将注解解释器关联；自定义注解解释器的时候实现 ConstraintValidator<A extends Annotation, T>接口。

