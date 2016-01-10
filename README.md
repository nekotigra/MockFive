# MockFive Installation and Use
Add `pod 'MockFive'` to your Podfile or drag `MockFive.swift` from the `MockFive` folder into your project.

Make your mock object conform to `Mock` and add `let mockFiveLock = lock()` anywhere in the struct or class.  Now you can begin using `mock()`!  Call `mock()` within a function body to record its invocation in `myMockObject.invocations`, and to stub in alternative implementations at runtime.

# Manual Mocking Made Easier
The most common task for a mock object is to report what functions have been invoked on it.  Because MockFive uses reflection to generate its messages, the `mock()` method guarantees that testing strings always match production code.  The `Mock` protocol may be adopted by both struct mocks and class mocks.

# Examples
Protocol Mocking
```Swift
import MockFive

struct CustomModel { var id: Int }

protocol MockworthyProtocol {
    func method()
    func complexMethod(arg: Int, model: CustomModel, others: Any?...) -> (Int, String)
}

struct MockImplementation: MockworthyProtocol, Mock {
    let mockFiveLock = lock()
    
    func method() { mock(identifier: "method") }
    func complexMethod(arg: Int, model: CustomModel, others: Any?...) -> (Int, String) {
        mock(identifier: "complexMethod", args: arg, model.id, others) { (37, "stub string") }
    }
}

var mock = MockImplementation()

// Invocation records
mock.method()
mock.complexMethod(7, model: CustomModel(id: 982), others: 7, nil, 0.23, [0,9]) // (37, "stub string")

mock.invocations[0] // "method()"
mock.invocations[1] // "complexMethod(_: 7, model: 982, others: [Optional(7), nil, Optional(0.23), Optional([0, 9])]) -> (Int, String)"

// Function stubbing
mock.registerStub("complexMethod") { (90, "Total \(42 + 9)") }
mock.complexMethod(9, model: custom) // (90, "Total 51")
```

Class Mocking
```Swift
import MockFive

struct CustomModel { var id: Int }

class MockworthyClass {
    func method()
    func complexMethod(arg: Int, model: CustomModel, others: Any?...) -> (Int, String)
}

struct MockImplementation: MockworthyClass, Mock {
    let mockFiveLock = lock()
    
    func method() { mock() }
    func complexMethod(arg: Int, model: CustomModel, others: Any?...) -> (Int, String) {
        return mock(arg, model.id, others) { (37, "stub string") }
    }
}

// Invocation records
var mock = MockImplementation()
mock.method()
mock.complexMethod(7, model: CustomModel(id: 982), others: 7, nil, 0.23, [0,9]) // (37, "stub string")

mock.invocations[0] // "method()"
mock.invocations[1] // "complexMethod(_: 7, model: 982, others: [Optional(7), nil, Optional(0.23), Optional([0, 9])]) -> (Int, String)"

// Function stubbing
mock.registerStub("complexMethod") { (90, "Total \(42 + 9)") }
mock.complexMethod(9, model: custom) // (90, "Total 51")
```
See more examples in `TestTrack.playground` in the project!


