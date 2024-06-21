---
title:       "RxSwift MVVM最佳实践：Inputs - Outputs（翻译）"
subtitle:    ""
description: ""
date:        2018-05-10
author:      ""
image:       ""
tags:        ["MVVM", "Inputs - Outputs"]
categories:  ["iOS" ]
---

当谈到iOS架构时，MVVM是个很好的选择。它不仅提供了比MVC更好的可测试性，而且与VIPER等同类架构相比，这个架构是轻量级的。尽管如此，应该采用适当的方法来利用MVVM。否则，我们最终可能会使用一个类似的MVC版本，并添加一个额外的组件(ViewModel)。

这篇文章介绍了一种叫Inputs - Outputs的方法，目前在Kickstarter上使用。在Kickstarter iOS应用中你可以看到大多以这种风格的编程方式。

#### Principles 原则
- Inputs是一组对ViewModel有影响的操作和事件，比如按钮上的tap操作，或者viewDidLoad事件。
- Outputs表示视图需要去响应的变化。(译者注：比如数据的改变)
- 由于ouput可能会随时间变化，所以最好在每个ouput中返回一个可观察的(在RxSwift上下文中)。
- 在output中定义的行为不应该被表示为变量，因为我们不需要可观察的输入。

#### How-to? 怎么做
``` swift
protocol LoginViewModelInputsType {
	func viewDidLoad()
	func tapSubmit()
	func type(email: String)
	func type(password: String)
}

protocol LoginViewModelOutputsType {
	var validInput: Observable<Bool> { get }
	var isLoading: Observable<Bool> { get }
	var loginSuccess: Observable<Void> { get }
	var loginFailure: Observable<ErrorMessage> { get }
}

protocol LoginViewModelType {
	var inputs: LoginViewModelInputsType { get }
	var ouputs: LoginViewModelOutputsType { get }
}
```
这就是LoginViewModel的样子:
``` swift
final class LoginViewModel: LoginViewModelType, LoginViewModelInputsType, LoginViewModelOutputsType {
	var inputs: LoginViewModelInputsType { return self }
	var ouputs: LoginViewModelOutputsType { return self }

	// MARK: - Inputs
	private let _tapSubmit = Variable<Void>(())
	func tapSubmit() { 
		_tapSubmit.value = ()
	}

	private let _email = Variable<String>("")
	func type(email: String) {
		_email.value = email
	}
	...

	// MARK: - Ouputs
	private let _loginSuccess = Variable<Void>(())
	var loginSuccess: Observable<Void> { return _loginSuccess.skip(1) }
	...

	init() {
		let loginObservable = _tapSubmit.asObservable().skip(1)
			.flatMapLatest(login)
			.share()

		loginObservable
			.bind(to: _loginSuccess)
			.diposed(by: disposeBag)

		// Binding for `_loginFailure` and `isLoading` goes here
		...
	}
}
```
#### Why Inputs - Outputs? 为什么使用Inputs - Outputs
首先，通过使用这样的协议，我们实现了更高层次的抽象。因此，我们的代码更加面向行为，更容易测试。

这种基于协议的约定的另一个优点是单元测试的可读性。让我们来看看下面这两个简单的测试:
``` swift
func test_When_PasswordIsEmpty_Then_InputIsInvalid() {
	viewModel.inputs.viewDidLoad()
	viewModel.inputs.type(email: "abc@xyz.com")

	// `grabLatestValue` is just an utility function we can write to retrieve
	// the latest value in the stream (during a specific period of time).
	// `RxBlocking` comes for the rescue.
	let validInput = grabLatestValue(viewModel.outputs.validInput, duration: 1)
	expect(validInput).to(beFalse())
}

func test_When_Submitting_Then_ShouldShowLoadingAndThenHideWhenCompleted() {
	viewModel.inputs.viewDidLoad()
	viewModel.inputs.type(email: "abc@xyz.com")
	viewModel.inputs.type(password: "Password0")
	viewModel.inputs.tapSubmit()

	let loadingStates = grabLatestValue(viewModel.outputs.isLoading, duration: 1)
	expect(loadingStates).to(beEqual([true, false]))
}
```
通过查看与输入调用相关的代码，我们很快就能了解我们试图模拟的场景。同样，我们期望看到的是反映在输出上。
##### 引用
[1] [https://github.com/kickstarter/native-docs/blob/master/vm-structure.md](https://github.com/kickstarter/native-docs/blob/master/vm-structure.md)
[2] [https://github.com/kickstarter/native-docs/blob/master/inputs-outputs.md](https://github.com/kickstarter/native-docs/blob/master/inputs-outputs.md)

原文：
https://trinhngocthuyen.github.io/2017-12-20-MVVM-best-practice-Inputs-Outputs.html
