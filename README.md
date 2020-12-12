# Writing Unit Tests In Angular With Jasmine

How To Write Unit Tests For Angular With Jasmine

# Contents
----
- [Setting Up ESLint In Visual Studio Code](#Setting-Up-ESLint-In-Visual-Studio-Code)
- [Setting Up Code Coverage Checker In Visual Studio Code](#Setting-Up-Code-Coverage-Checker-In-Visual-Studio-Code)
- [Variable Naming Conventions](#Variable-Naming-Conventions)
- [Filename Naming Conventions](#Filename-Naming-Conventions)
- [General Good Practises](#General-Good-Practises)
- [Writing Unit Tests](#Writing-Unit-Tests)
  - [Test Names](#Test-Names)
  - [Checking A Function Is Called Using Spies](#Checking-A-Function-Is-Called-Using-Spies)
  - [Change The Return Value Of A Function](#Change-The-Return-Value-Of-A-Function)
  - [Change The Return Value Of A Getter](#Change-The-Return-Value-Of-A-Getter)
  - [TS Tests](#TS-Tests)
      1. [Checking Functions Are Called](#1\.-Checking-Functions-Are-Called)
      1. [Checking Fields Are Updated](#2\.-Checking-Fields-Are-Updated)
      1. [Checking The Outputs For Each Input](#3\.-Checking-The-Outputs-For-Each-Input)
      1. [Checking Subscriptions](#4\.-Checking-Subscriptions)
      1. [Checking Getters And Setters](#5\.-Checking-Getters-And-Setters)
  - [HTML Tests](#HTML-Tests)
      1. [Checking Components Are Displayed](#1\.-Checking-Components-Are-Displayed)
      2. [Checking Text Is Displayed](#2\.-Checking-Text-Is-Displayed)
      3. [Testing `*ngIfs`](#3\.-Testing-`*ngIfs`)
      4. [Testing `*ngFors`](#4\.-Testing-`*ngFors`)
      5. [Testing `[ngClass]`](#5\.-Testing-`[ngClass]`)
      6. [Checking Functions Are Called When Child Component Output Events Trigger](#6\.-Checking-Functions-Are-Called-When-Child-Component-Output-Events-Trigger)
      7. [Checking Child Components Are Passed The Correct Inputs](#7\.-Checking-Child-Components-Are-Passed-The-Correct-Inputs)
      8. [Testing User Inputs Update The Correct Field](#8\.-Testing-User-Inputs-Update-The-Correct-Field)
  - [Testing Asynchronous Code Using Rxjs](#Testing-Asynchronous-Code-Using-Rxjs)
      -  [Testing Navigation](#Testing-Navigation)
      -  [Delayed Observables](#Delayed-Observables)
      -  [SetTimeout](#SetTimeout)
      -  [Modals](#Modals)
  - [Testing Functions Are Called In The Constructor Of A Component](#Testing-Functions-Are-Called-In-The-Constructor-Of-A-Component)
  - [Testing On Push Components](#Testing-On-Push-Components)
  - [Testing The Change Detector And Components With A Detached Change Detector](#Testing-The-Change-Detector-And-Components-With-A-Detached-Change-Detector)
  - [Testing Local Storage](#Testing-Local-Storage)
  - [Testing Third Party Components](#Testing-Third-Party-Components)
      - [Testing The ng-select Component](#Testing-The-ng-select-Component)
      - [Testing With Immutable.js Data](#Testing-With-Immutable.js-Data)
  - [Helpful Tips](#Helpful-Tips)
      - [Run A Single Unit Test Or Spec File](#Run-A-Single-Unit-Test-Or-Spec-File)
      - [UI](#UI)
      - [Simulating Key Presses](#Simulating-Key-Presses)
      - [Unit Test Errors On Azure But Not Locally](#Unit-Test-Errors-On-Azure-But-Not-Locally)
      - [Errors With Functions Being Undefined](#Errors-With-Functions-Being-Undefined)
      - [Problems With The Expected Result Changing](#Problems-With-The-Expected-Result-Changing)
      - [DebugElement, NativeElement and ComponentInstance](#DebugElement,-NativeElement-and-ComponentInstance)

- [Writing Integration Tests](#Writing-Integration-Tests)

## Setting Up ESLint In Visual Studio Code
---
We use ESLint to automatically check the syntax, formatting and conventions of our code. When you run `ng lint` it will go through all the syntax rules in `.eslintrc.js` and check our code against them. However you can set up ESLint to hightlight any potential issues within the editor as you type your code.

Within Visual Studio Code, install the ESLint extension and make sure to remove the TSLint extension. You can do this only for this project, so you don't affect the installed extensions for other projects/repos. 

You can also set up Visual Studio to automatically reformat and indent your code to align with the ESLint rules when you save a file. Go to File->Preferences->Settings. In the search bar, type 'save' and check the Format On Save box. You can also decide whether you want to format the file on save or the whole project.

![image.png](/.attachments/image-da28d52d-241b-4ea9-baa3-df6ffb551ab8.png)

Then go to the settings for the ESLint extension and Enable ESLint as a formatter.

![image.png](/.attachments/image-5bbece38-88be-432e-ba98-2d05ae7e5963.png)

## Setting Up Code Coverage Checker In Visual Studio Code

There is also a useful extension in Visual Studio Code that reveals the code coverage for your code while within the IDE. Search for 'code coverage' in the extensions and look for this extension by Markis Taylor.

![image.png](/.attachments/image-0cec6282-dc2d-40f4-a280-4f6238d45d74.png)

It will highlight the lines that are not covered by unit tests.

![image.png](/.attachments/image-117dab5f-3824-4767-9a65-a00dbdf30e72.png)

## Writing Unit Tests
---

In short, you should test everything that could possibly change or cause some event or function to be called. We do not test every static element and every single class that is applied to each element. But we do test that the correct child components we created are present and any classes that vary i.e ```[ngClass]```.

We use ```testing-utils.ts``` to provide a reusable ```TestBed``` where we import all the shared modules and mock all the services. We then import this ```TestBed``` into the unit test spec file for each component and add what we need to it. This ensures we only use mocked services in each component and that we don’t have to redefine these mocked services in every component.

For each unit test we then only have to add the declaration for the component we are testing and declarations for any mocked child components. We shouldn’t have to redefine the mocked services here. If we need to change what a function of a service returns for a particular unit test we can use a jasmine spy and change the return value temporarily.

  - [Test Names](#Test-Names)
  - [Checking A Function Is Called Using Spies](#Checking-A-Function-Is-Called-Using-Spies)
  - [Change The Return Value Of A Function](#Change-The-Return-Value-Of-A-Function)
  - [TS Tests](#TS-Tests)
  - [Testing Asynchronous Code Using Rxjs](#Testing-Asynchronous-Code-Using-Rxjs)
  - [Testing On Push Components](#Testing-On-Push-Components)
  - [Testing Local Storage](#Testing-Local-Storage)
  - [Testing Third Party Components](#Testing-Third-Party-Components)
  - [Helpful Tips](#Helpful-Tips)

### Test Names

Whenever you are naming your tests you should try to make it as user friendly as possible as if you are describing what it does to someone else. The name should follow the pattern 'it should do something when doing something'. In Angular you don't usually put the function names in the test names.

``` ts
it('should ? when ?', () => {

})
```

Some Examples

It should load the projects when initialising
It should set the tenant name when loading the tenant config
It should get the token when checking the user is logged in
It should reload the projects when clicking on the reload button
It should show a toast notification when there is a tenant config api error
It should not show the 10x logo when the tenant is not 10x
It should not remove the refresh token when logging out

#### Checking A Function Is Called Using Spies

We can check a function is called during another function by using spies. We first spy on a listen for calls to a function. Then execute the function that should call this function. Then we check the function we spied on was called.

``` ts
const spy = spyOn(component, 'functionToBeCalled');

component.function();

expect(spy).toHaveBeenCalledWith()  // or toHaveBeenCalledWith('something')
```

Using `toHaveBeenCalledWith()` we can be even more specific and check what was passed to the function. We can also use `spy.calls.mostRecent().args` to grab the arguments themselves and test them this way. For example

``` ts
const spy = spyOn(component, 'functionToBeCalled');

component.function();

expect(spy.calls.mostRecent().args[0]).toEqual('argument1')
```

We can also spy on the functions of services. But we first have to get a reference to the service in our test. The service first needs to be added to the TestBed in testing-utils.ts. We can then grab that service using `TestBed.inject()`.

``` ts
const apiService = TestBed.inject(ApiService)
const spy = spyOn(apiService, 'functionToBeCalled');

component.function();

expect(spy).toHaveBeenCalledWith()  // or toHaveBeenCalledWith('something')
```

#### Change The Return Value Of A Function

In testing-utils.ts we fix the return values of functions. But suppose we want to test that something happens when that return value is different. We can just temporarily change the return value of a function. In this example we change the `http.get()` function to return an error whenever it is called.

``` ts
import { throwError } from 'rxjs';

const httpClient = TestBed.inject(HttpClient);
spyOn(httpClient, 'get').and.returnValue(throwError(new Error()));
```

Note that when you create a spy you are actually replacing the function and if something is expecting a return value from this function and you haven't set one, you'll run into errors.

#### Change The Return Value Of A Getter

You can also change the return value of a getter on some other service. Suppose you have something like the following in a service called `DataService`.

``` ts
public get data() {
    return this._data;
}
```

In your unit tests you can change the return value by using `spyOnProperty`.

``` ts
const dataService = TestBed.inject(DataService);
spyOnProperty(dataService,'data').and.returnValue('test data');
```

### TS Tests
---

When you write unit tests for TS functions you do not have to test all the inner workings of a function just that different inputs result in the right functions being called and the right fields being changed and that you get the right output for each input. You should test enough inputs that every part of the function is executed. Don't forget to test `component.ngOnInit()` and `component.ngOnDestroy()` aswell. You should also test any observables that are subscribed to, even the ones in the constructor and `ngOnInit()`. You should be

1. [Checking Functions Are Called](#1\.-Checking-Functions-Are-Called)
1. [Checking Fields Are Updated](#2\.-Checking-Fields-Are-Updated)
1. [Checking The Outputs For Each Input](#3\.-Checking-The-Outputs-For-Each-Input)
1. [Checking Subscriptions](#4\.-Checking-Subscriptions)
1. [Checking Getters And Setters](#5\.-Checking-Getters-And-Setters)

#### 1. Checking Functions Are Called

A function might call another component function or the function of a service. To test this we just spy on the function we expected to be called.

``` ts
const spy = spyOn(component,'functionToBeCalled');

component.loadData();

expect(spy).toHaveBeenCalledWith();
```

You can also check that functions are not called

``` ts
const spy = spyOn(component,'functionToBeCalled');

component.loadData();

expect(spy).not.toHaveBeenCalledWith();
```

You can also check the spy this way

``` ts
spyOn(component,'functionToBeCalled');

component.loadData();

expect(component.functionToBeCalled).toHaveBeenCalledWith();
```

You can also spy on private functions by setting the return type of the spy to `any` and using object key notation instead of dot notation.

``` ts
spyOn<any>(component,'_privateFunction');

component.loadData();

expect(component['_privateFunction']).toHaveBeenCalledWith();
```

#### 2. Checking Fields Are Updated

A function might update some fields as its executed. To test this we initialise that field, execute the function and check the value of that field.

``` ts
component.count = 5;
const newCount = 10;

component.updateCount(newCount);

expect(component.count).toEqual(newCount);
```

#### 3. Checking The Outputs For Each Input

If the function returns a value we should be checking that the function returns the correct value for each input we try. Its up to you to decide what and how many inputs to try. Your unit test might look something like this.

``` ts
const input = 5;
const expectedResult = 10;

const result = component.double(input);

expect(result).toEqual(expectedResult);
```

#### 4. Checking Subscriptions

You also need to test that the right functions are called and fields are updated when an observable outputs a new value. Suppose an observable was subscribed to during `ngOnInit()` like below.

``` ts
ngOnInit() {
    this.apiService.getData().subscribe(result => {
        this.handleData(result);
        this.data = result;
    })
}
```

We need to check that the function `handleData()` is called and that the field `data` is set. We first need to ensure that the subscription is created. In this case we are dealing with `ngOnInit()`, and it should be executed before each unit test already so we don't have to call the function `component.ngOnInit()` for this example. Now we need to emit a new output event. If `getData()` returns a subject we can just grab that subject on the mocked service and emit a new value. But if `getData()` returns an observable then we may have to create a new subject field on the mocked service and set `getData()` up to return this subject as an observable. In testing-utils.ts we can change the mocked service.

``` ts
ApiServiceMock {
    private getDataSubject = new Subject();
    public getData() {
        return this.getDataSubject.asObservable();
    }
}
```

Then in the unit test we can just grab `getDataSubject` and emit a new value. The unit test for checking the function is called might be

``` ts
const apiService = TestBed.inject(ApiService);
const spy = spyOn(component,'handleData')

apiService['getDataSubject'].next('test value');

expect(spy).toHaveBeenCalledWith('test value');
```

You might also have the situation where we have a variable subscription. We might want to subscribe to two different things depending on some condition but then do the same thing with that data so we assign the sub Suppose we have something like this.

``` ts
public getData(getThisData) {

    let action: Observable<string>;
    if (getThisData) {
        action = this.apiService.getThisData();
    } else {
        action = this.apiService.getThatData();
    }

    action.subscribe((data) => {
        this.dataService.handleData(data)
    });
}
```

To test that the `handleData()` method is called when the subscription is successful we need to create a new subject and spy on the `getThisData()` and `getThatData()` and set up these methods to return this subject as an observable. Then we can emit a new value from this subject and test `handleData()` is called. The unit test might be.

``` ts
it('should handle the data when getting this data', () => {
    const apiService = TestBed.inject(ApiService);
    const dataService = TestBed.inject(DataService);
    const actionSubject = new BehaviorSubject<string>(null);
    spyOn(apiService,'getThisData').and.returnValue(actionSubject.asObservable());
    spyOn(dataService,'handleData');
    component.getData(true);

    actionSubject.next('test data');

    expect(dataService.handleData).toHaveBeenCalledWith('test data');
})
```

#### 5. Checking Getters And Setters

Suppose you had the following getter method on your component.

```
public get getData(): Data {
    return this._data;
}
```

To test it, you just simply call the getter like any other function but don't add parentheses.

```
const result = component.getData;
```

And suppose you have the following setter.

```
public set setData(data) {
    this._data = data;
}
```

To test this you call it by assigning a value to it.

```
component.setData = data;
```

### HTML Tests

---

Writing HTML tests is a lot more complicated than writing TS tests, there are a lot more things to test. We need to test that the important components are present and displayed in the UI and that any important text is displaying correctly in the UI. We need to test every `*ngIf` and that components are displaying or not displaying depending on the value of the fields. We also test every `*ngFor` and that the correct number of components are being displayed and the values of those elements are correct. We also should test classes that vary with `[ngClass]` and that the correct classes are being applied or not being applied depending on the components fields. We also need to check that the correct functions are called when a child component emits an output event and that child components are passed the correct inputs. Lastly, we check fields that are bound to child components and that when the user updates an input on the UI that these fields on the component are updated. You should be

 1. [Checking Components Are Displayed](#1\.-Checking-Components-Are-Displayed)
 2. [Checking Text Is Displayed](#2\.-Checking-Text-Is-Displayed)
 3. [Testing `*ngIfs`](#3\.-Testing-`*ngIfs`)
 4. [Testing `*ngFors`](#4\.-Testing-`*ngFors`)
 5. [Testing `[ngClass]`](#5\.-Testing-`[ngClass]`)
 6. [Checking Functions Are Called When Child Component Output Events Trigger](#6\.-Checking-Functions-Are-Called-When-Child-Component-Output-Events-Trigger)
 7. [Checking Child Components Are Passed The Correct Inputs](#7\.-Checking-Child-Components-Are-Passed-The-Correct-Inputs)
 8. [Testing User Inputs Update The Correct Field](#8\.-Testing-User-Inputs-Update-The-Correct-Field)

#### 1. Checking Components Are Displayed

To test a particular component is showing in the UI we can grab the element by using the query function on the debugElement. We can then use the built in class `By` to grab the elements by their id or class or their selector. We can even grab elements by Directive. Add ids to elements if you have to, but do not change existing ids unless you absolutely have to as this can break the integration tests.
``` ts
// Get an element by the selector
const brandSelectorComponent = componentFixture.debugElement.query(By.css('app-brand-selector'));
// Get an element by its id
const element = componentFixture.debugElement.query(By.css('#someId'));
// Get an element by its class or classes
const element = componentFixture.debugElement.query(By.css('.class-one'));
const element = componentFixture.debugElement.query(By.css('.class-one.class-two'));
// Get an element by the component class
const slider = componentFixture.debugElement.query(By.directive(SliderComponent));
```

You can then just check that the query found something using toBeTruthy

``` ts
expect(element).toBeTruthy();
```

Only use ```toBeTruthy()``` and ```toBeFalsy()``` when checking a component exists. Don’t use them when checking inputs and fields.

If you want to grab multiple elements you can use `querySelectorAll()`. This is useful for testing ngFors. If you want to check the right number of elements are displaying you can do the following

``` ts
const elements = componentFixture.debugElement.nativeElement.querySelectorAll('.rows');
expect(elements.length).toEqual(3);
```

Theres also `document.querySelector()` but we shouldn't use this as it searches over the whole UI document whereas when we call `query()` or `querySelectorAll()` we search within the component itself.

#### 2. Checking Text Is Displayed

If you want to check that some text is displaying in the UI you just grab that element and check its `innerText`.

``` ts
const element = componentFixture.debugElement.query(By.css('#someId'));
expect(component.innerText).toEqual('Welcome back!');
```

And using `querySelectorAll()`

``` ts
component.messages = ['a','b','c'];
componentFixture.detectChanges();
const elements = componentFixture.debugElement.nativeElement.querySelectorAll('.rows');
for (let i = 0; i < component.messages.length; i++) {
    expect(elements[i].innerText).toEqual(component.messages[i]);
}
```

#### 3. Testing `*ngIfs`

When testing ngIf we want to write at least a couple of unit tests. One checking that the component is displaying and one checking its not displaying depending on the value of the field in the condition. First we set the value of the field then update the UI by calling `detectChanges()` and then check the component exists.

Suppose we have something like

``` html
<ng-container *ngIf="showTitle">
    <div id="title">A Title</div>
</ng-container>
```

Then our unit tests would be

``` ts
component.showTitle = true;

componentFixture.detectChanges();

const element = componentFixture.debugElement.query(By.css('#title'));
expect(element).toBeTruthy();
```
``` ts
component.showTitle = false;

componentFixture.detectChanges();

const element = componentFixture.debugElement.query(By.css('#title'));
expect(element).toBeFalsy();
```

If there is more than one field in the ngIf then we should check all the possible different combinations.

``` html
<ng-container *ngIf="showTitle && somethingElse">
    <div id="title">A Title</div>
</ng-container>
```

If the condition depends on the result of some function then you can just change the return value of that function and check the UI.

``` html
<ng-container *ngIf="showTitle()">
    <div id="title">A Title</div>
</ng-container>
```

The unit tests might be

``` ts
spyOn(component,'showTitle').and.returnValue(true);

componentFixture.detectChanges();

const element = componentFixture.debugElement.query(By.css('#title'));
expect(element).toBeTruthy();
```

``` ts
spyOn(component,'showTitle').and.returnValue(false);

componentFixture.detectChanges();

const element = componentFixture.debugElement.query(By.css('#title'));
expect(element).toBeFalsy();
```

#### 4. Testing `*ngFors`

If you want to grab multiple elements you can use `querySelectorAll()`. This is useful for testing ngFors. If you want to check the right number of elements are displaying you can do the following

``` ts
const elements = componentFixture.debugElement.nativeElement.querySelectorAll('.rows');
expect(elements.length).toEqual(3);
```

And suppose you want to check the text displayed in the UI.

``` ts
component.messages = ['a','b','c']
const elements = componentFixture.debugElement.nativeElement.querySelectorAll('.rows');
for (let i = 0; i < component.messages.length; i++) {
    expect(elements[i].innerText).toEqual(component.messages[i]);
}
```

#### 5. Testing `[ngClass]`

To test that the correct classes are being applied to elements we do exactly the same thing as do we when testing ngIfs and write at least two unit tests. One for when the class is applied and one for when it isn't.  We set the value of the field and then update the UI and grab the element and check its classes.

Suppose we have

``` html
<div id="title" [ngClass]="{'filled': filled}">A Title</div>
```

Then the unit tests might be

``` ts
component.filled = true;

componentFixture.detectChanges();

const element = componentFixture.debugElement.query(By.css('#title')).nativeElement;
expect(element.classList).toContain('filled');
```
``` ts
component.filled = false;

componentFixture.detectChanges();

const element = componentFixture.debugElement.query(By.css('#title')).nativeElement;
expect(element.classList).not.toContain('filled');
```

And when using `querySelectorAll()` you can grab the class list this way

``` ts
const elements = componentFixture.debugElement.nativeElement.querySelectorAll('#title');
expect(elements[0].classList).toContain('filled');
```

#### 6. Checking Functions Are Called When Child Component Output Events Trigger

To check that the right functions are called when a certain output event triggers on a child component we can use `dispatchEvent()` to simulate the event. Suppose we have the following element with an output event.

``` html
<app-team id="team" (changeTeam)="updateTeam($event)"></app-team>
```

The unit test might be

``` ts
const spy = spyOn(component,'updateTeam');

const elements = componentFixture.debugElement.nativeElement.querySelectorAll('#title');
elements[0].dispatchEvent(new Event('changeTeam'));

expect(spy).toHaveBeenCalledWith(jasmine.any(Event));
```

#### 7. Checking Child Components Are Passed The Correct Inputs

We also should check that a component passes the right inputs to its child components. Suppose we have a component with the following template html. It contains a child component called `TeamDropdown` with the selector `app-team-dropdown` which has inputs.

``` html
<app-team-dropdown
    id="team"
    [items]="teams"
    [value]="currentTeam"
    [disabled]="teamsDisabled"
></app-team-dropdown>
```

If the child component is our own custom component then we need to make sure to add a mocked version of the component in testing-utils.ts. The mocked component for this might be.

``` ts
@Component({
    selector: 'app-team-dropdown',
    template: '',
})
class TeamDropdown {
    private _items;
    @Input() public set items(value) {
        if (value) {
            this._items = value;
        }
    }
    @Input() public value;
    @Input() public disabled;
}
```

When testing inputs to child components we grab the component using the `componentInstance` to be able to get access to the data on that component.

``` ts
const teamSelectorComponent = componentFixture.debugElement.query(By.css('#team')).componentInstance;
expect(teamSelectorComponent.['_items']).toEqual(component.teams);
expect(teamSelectorComponent.value).toEqual(component.currentTeam);
expect(teamSelectorComponent.disabled).toEqual(component.teamsDisabled);
```

Note: We do not create a test wrapper parent component and test that its properties are passed down to the component we want to test as those parent properties are meaningless. We just made those up. We want to test each real component's properties are passed down to its child components correctly.

If we are testing a built in component like `<input>` then we just use nativeElement.

#### 8. Testing User Inputs Update The Correct Field

We should also simulate the users interactions with a component and check that when they set the value of dropdowns, inputs and text areas that the correct field is updated. In the example below we want to check that jobTitle is updated when the user inputs a new value.

``` html
<input id="txtJobTitle" type="text" [(ngModel)]="jobTitle">
```

We first initialise the value of the jobTitle field and then grab the element and set its new value and then simulate the input event. Then check the field was updated. The unit test for this might be.

``` ts
component.jobTitle = null;

const jobTitleComponent = componentFixture.debugElement.query(By.css('#txtJobTitle')).nativeElement;
jobTitleComponent.value = 'new job title';
jobTitleComponent.dispatchEvent(new Event('input'));

expect(component.jobTitle).toEqual('new job title');
```

And with using `querySelectorAll()`

``` ts
component.jobTitle = null;

const jobTitleComponents = componentFixture.debugElement.nativeElement.querySelectorAll('#txtJobTitle');
jobTitleComponents[0].value = 'new job title';
jobTitleComponents[0].dispatchEvent(new Event('input'));

expect(component.jobTitle).toEqual('new job title');
```

#### Testing Asynchronous Code Using Rxjs

When you want to test asynchronous code then you need to wrap your unit test in `fakeAsync()` and then call `tick()` to simulate time passing and skip ahead to when all the asynchronous code has completed.

``` ts
it('should ? when ?', fakeAsync(() => {
    ...

    tick(); // Wait until asynchronous code is complete
    expect(result).toBe(expected);
}));
```

- [Testing Navigation](#Testing-Navigation)
- [Delayed Observables](#Delayed-Observables)
- [SetTimeout](#SetTimeout)
- [Modals](#Modals)

##### Testing Navigation

An example of asynchronous code would be navigation. You want to test that you end up at a different path when you navigate. Suppose you try to navigate to `/home` with the following Routes.

``` ts
export const appRoutes: Routes = [
    {
        path: 'home',
        component: HomeComponent,
        canActivate: [AuthGuardRoleService]
    }
]
```

You set the `canActive()` function to return true, so that you are allowed to get to `/home`, and then try navigating. This is 

``` ts
it('should navigate to the home page if user is logged in', fakeAsync(() => {
    const router = TestBed.inject(Router);
    const authGuardRoleService = TestBed.inject(AuthGuardRoleService);

    spyOn(authGuardRoleService, 'canActivate').and.returnValue(true);
    router.navigate(['home']); // Start navigating
    tick(); // Wait until the navigating is complete before testing the url

    expect(router.url).toBe('/home');
}));
```

##### Delayed Observables

Suppose we have a constructor where we subscribe to some observable representing what the user is entering into a search input and want to call a function `getFirstPage()` once the user stops typing. We can add `debounceTime(500)` to only call the function when the user hasn't typed anything for the last half a second.

``` ts
constructor() {
    this.getUserSearch().pipe(debounceTime(500)).subscribe(() => {
        this.getFirstPage();
    });
}
```

The unit test to check the function is called will be

``` ts
it('should get the first page when the user types into the search input', fakeAsync(() => {
    const spy = spyOn(component,'getFirstPage');
    const searchString = 'abc';

    component['_userSearchSubject'].next(searchString); // emit a new input value
    tick(500); // wait for half a second before checking the function is called

    expect(spy).toHaveBeenCalledWith();
}));
```

Notice that just using `tick()` won't work here, instead we have to specify the exact amount of time `tick(500)` we want to wait for before checking the function is called.

#### SetTimeout

This is similar to the delayed observables. `setTimeout(() => {},x)` waits for `x` milliseconds before executing the function and so when we test the code it contains we need to wrap our unit test with `fakeAsync()` and use `tick(x)` to wait `x` milliseconds first before testing functions are called and fields have been updated.

#### Modals

When we are dealing with a component that uses a modal, we need to make sure that the modal is displaying first otherwise we won't find anything when we try to grab it. Note that modals don't appear under the normal DOM tree under the root component, but instead appear next to root in the DOM tree so you can't check a parent component contains it.

In the `beforeEach()` you can make sure that the `display` css attribute is set correctly and apply any classes you need. Then call `detectChanges()` to update the UI.

``` ts
beforeEach(() => {
    componentFixture = TestBed.createComponent(ProjectTemplatePickerComponent);
    component = componentFixture.componentInstance;
    componentFixture.detectChanges();

    // Display the modal, initially it isn't
    const projectTemplatePickerModalComponents = componentFixture.debugElement.nativeElement.querySelectorAll('#projectTemplatePickerModal');
    projectTemplatePickerModalComponents[0].style.display = 'block';
    projectTemplatePickerModalComponents[0].classList.add('c-modal--open');
    componentFixture.detectChanges();
});
```

#### Testing Functions Are Called In The Constructor Of A Component

To test functions are called within the constructor of a component you must use the TestBed to create a new component to trigger the constructor again as you can't just call the constructor like a normal method. Suppose we have the constructor below for the component called `MyComponent`.

``` ts
constructor(
    private _apiService: ApiService,
) {
    this._apiService.getData();
}
```

The unit test might be

``` ts
it('should get the data from the api on construction', () => {
    const apiService = TestBed.inject(ApiService);
    spyOn(apiService,'getData');

    TestBed.createComponent(MyComponent);

    expect(apiService.getData).toHaveBeenCalledWith();
});
```

#### Testing The Change Detector Functions Are Called In The Constructor Of A Component

Checking the change detector's functions are called in the constructor is a special case as ChangeDetectorRef is not like a normal service. Instead of using the TestBed to grab a reference to the change detector we have to use the injector on the component itself and then we spy on the change detector prototype's functions. Suppose we have the following constructor for the component called MyComponent. 

``` ts
constructor(
    private _cdr: ChangeDetectorRef,
) {
    this._cdr.detach();
}
```

The unit test might be

``` ts
it('should detach from the change detector on construction', () => {
    const changeDetectorRef = componentFixture.debugElement.injector.get(ChangeDetectorRef); 
    const detectChangesSpy = spyOn(changeDetectorRef.constructor.prototype, 'detach');

    TestBed.createComponent(MyComponent);

    expect(detectChangesSpy).toHaveBeenCalledWith();
});
```

#### Testing On Push Components

On push components are those that have a change detection strategy set to OnPush. This means that the UI only updates when the fields are assigned new references. So it will update if an immutable field is updated like a string, but not if a mutable field is updated like an array. This helps to prevent the UI keep updating with every little change, which ends up making it more unresponsive. 

``` ts
@Component({
    selector: 'app-solution-dashboard',
    templateUrl: './solution-dashboard.page.html',
    changeDetection: ChangeDetectionStrategy.OnPush
})
export class SolutionDashboardComponent implements OnInit, OnDestroy {
```

At the moment there is no easy way to test an OnPush component. There is currently a bug in Angular with `componentFixture.detectChanges()` only updating the UI the first time you use it, but not work thereafter. 

https://github.com/angular/angular/issues/12313

This prevents us from writing unit tests to check the UI is updated with the correct data. The current workaround at the moment is just to disable the OnPush functionality in the unit tests completely and set the Change Detection Strategy back to the default mode. This means we can't properly test the OnPush functionality of components but theres nothing we can do until the problem is fixed.

We can change the change detection strategy back to default when we set up the testing environment.

``` ts
beforeEach(async(() => {
    configureTestingModule({
        declarations: [
            SolutionDashboardComponent,
        ]
    })
    .overrideComponent(SolutionDashboardComponent, {
        set: {
            changeDetection: ChangeDetectionStrategy.Default
        }
    });
}));
```

Then we just test the component like normal.

#### Testing The Change Detector And Components With A Detached Change Detector

The change detector is what automatically updates the UI when the data and components fields are updated. Normally it checks for changes automatically and updates the UI after every single change in the values of the fields. However, when we set values for the components fields in the unit tests the UI doesn't automatically update and we have to call `componentFixture.detectChanges()` to manually update the UI.

Note: Because you don't deal with the UI in TS unit tests you don't have to call this function when writing TS tests.

We can also update the UI manually within the TS of the component by adding a reference to the change detector in the constructor and then calling the `detectChanges()` method on this.

``` ts
constructor(
    private _cdr: ChangeDetectorRef
) {
    this.loadData();
}

public loadData() {
    // Load the data
    ...
    // Update the UI when finished loading the data
    this._cdr.detectChanges();
}
```

Sometimes we just detach the change detector completely and stop it from automatically updating the UI and only update it manually ourselves when we want it to. For example, there may be loads of data changing frequently and we don't want to update the UI every single time otherwise it will become unresponsive. Instead, we can update it after all the data has stopped changing or only every so often.

But if we detatch it completely during initialisation then using `componentFixture.detectChanges()` within our unit tests will no longer work. 

``` ts
constructor(
    private _cdr: ChangeDetectorRef
) {
    // Detaches the component from change detection when constructed
    this._cdr.detach();
    this.loadData();
    this._cdr.detectChanges();
}
```

Instead we will need to grab a reference to the change detector that we have already included in the TS of the component and call `detectChanges()` on this instead. We just need to replace all

``` ts
componentFixture.detectChanges();
```

with

``` ts
component['_cdr'].detectChanges();
```

To check that the `cdr.detectChanges()` method is called within the TS of your component, in your unit tests you can spy on the change detector like this.

``` ts
const changeDetectorRef = componentFixture.debugElement.injector.get(ChangeDetectorRef);
const detectChangesSpy = spyOn(changeDetectorRef.constructor.prototype, 'detectChanges');

component.loadData();

expect(detectChangesSpy).toHaveBeenCalledWith();
```

#### Testing Local Storage

You can test local storage in unit tests aswell. To test that a function saves something to local storage you can do the following

``` ts
const spy = spyOn(Storage.prototype, 'setItem');
expectedSaved = { key: 'value' };

component.setData();

expect(spy).toHaveBeenCalledWith('data', JSON.stringify(expectedSaved));
```

To test that a function loads something up from local storage you can do

``` ts
const spy = spyOn(Storage.prototype, 'getItem').and.returnValue(JSON.stringify(tokenValid));

component.getTokenFromStorage();

expect(spy).toHaveBeenCalledWith("token");
```

### Testing Third Party Components

When it comes to testing third party components its best to check their documentation on how to test it. Usually they will have a GitHub page where you can see how they test the component themselves. But be careful here we don't want to test the way the component works, just that it is using and updating our component's fields correctly. We want to test that when the user interacts with it our fields are updated or functions are called.

- [Testing The ng-select Component](#Testing-The-ng-select-Component)
- [Testing With Immutable.js Data](#Testing-With-Immutable.js-Data)

#### Testing The ng-select Component

When testing that the user selects something in an ng-select component's dropdown and that a field is updated we need to wrap the unit test with `fakeAsync()` because the opening and closing of the dropdown is an asynchronous process and we will need to wait for the dropdown to open before simulating clicks on the dropdown. We can use the helper function triggerKeyDownEvent() to simulate the user the selecting different options.

``` html
<ng-select
    id="sltTimePeriodSolutionDashboard"
    [items]="sortOrders"
    [searchable]="false"
    [clearable]="false"
    [ngModel]="currentSort"
    (ngModelChange)="selectAllTimeValue($event)"
></ng-select>
```

``` ts
function triggerKeyDownEvent(element: DebugElement, which: number, key = ''): void {
    element.triggerEventHandler('keydown', {
        which: which,
        key: key,
        preventDefault: () => { },
    });
}

it('should update the components selected time value when the user updates the time period dropdown', fakeAsync(() => {
    componentFixture.detectChanges();
    const spy = spyOn(component, 'selectAllTimeValue');

    const timePeriodSelectorComponent = componentFixture.debugElement.query(By.css('#sltTimePeriodSolutionDashboard'));
    triggerKeyDownEvent(timePeriodSelectorComponent, 32); // space to open the ng select
    // We need to tick and detect changes as the dropdown fully initialises after the promise is resolved
    componentFixture.detectChanges();
    tick();
    triggerKeyDownEvent(timePeriodSelectorComponent, 40); // down arrow
    triggerKeyDownEvent(timePeriodSelectorComponent, 13); // enter

    flush();
    expect(spy).toHaveBeenCalledWith(jasmine.any(Object));
}));
```

We can also test the component the other way around using the inputs. We can test that updating the component's field updates the selected value on the component.

``` ts
it('should update the currently selected project template name dropdown when the components currentSort is changed', fakeAsync(() => {
    component.currentSort = testSort;
    componentFixture.detectChanges();
    tick();

    const timePeriodSelectorComponent = componentFixture.debugElement.query(By.css('#sltTimePeriodSolutionDashboard')).componentInstance;

    flush();
    expect(projectTemplateSortDropdownComponent.selectedItems[0].value).toEqual(testSort);
}));
```

#### Testing with immutable.js data

When we are working with data that uses the immutable.js libraries you need to compare values using the equality operator that comes with immutable.js, otherwise you might run into equality errors. For example suppose we had 
``` ts
import { List } from 'immutable';

component.data = List([]);
const expectedData = List([1,2,3])

component.setData(expectedData);

expect(component.data).toEqual(expectedData);
```

Instead we would write

``` ts
import { is, List } from 'immutable';

component.data = List([]);
const expectedData = List([1,2,3])

component.setData(expectedData);

expect(is(component.data,expectedData)).toEqual(true);
```

### Helpful Tips
---

- [Run A Single Unit Test Or Spec File](#Run-A-Single-Unit-Test-Or-Spec-File)
- [UI](#UI)
- [Simulating Key Presses](#Simulating-Key-Presses)
- [Unit Test Errors On Azure But Not Locally](#Unit-Test-Errors-On-Azure-But-Not-Locally)
- [Errors With Functions Being Undefined](#Errors-With-Functions-Being-Undefined)
- [Problems With The Expected Result Changing](#Problems-With-The-Expected-Result-Changing)
- [DebugElement, NativeElement and ComponentInstance](#DebugElement,-NativeElement-and-ComponentInstance)

#### Run A Single Unit Test Or Spec File

You can run the unit tests for just one file by replacing `describe` with `fdescribe` to focus only on those unit tests. Similarly, you can run an individual unit test by replacing `it` with `fit`. But make sure to remove these again when you are done.

#### UI

If you go to ```localhost:9876``` you can load up a UI that displays the components you are testing and the results.

#### Simulating Key Presses

You can simulate a key press on an element by using the following function


```
function triggerKeyDownEvent(element: DebugElement, which: number, key = ''): void {
    element.triggerEventHandler('keydown', {
        which: which,
        key: key,
        preventDefault: () => { },
    });
}

const component = componentFixture.debugElement.query(By.css('#someId'));
triggerKeyDownEvent(component, 32); // space
triggerKeyDownEvent(component, 40); // down arrow
triggerKeyDownEvent(component, 13); // enter
```

You use can use this website to find the keycode for any key.

https://css-tricks.com/snippets/javascript/javascript-keycodes/

#### Unit Test Errors On Azure But Not Locally

Note that when you create a PR and Azure runs the unit tests, it will run the unit tests as if the PR has already been merged with master. So you may encounter unit test errors on Azure that you don't locally. Be sure to pull down and merge in the lastest master branch so you can fix these.

#### Errors With Functions Being Undefined

Sometimes when adding unit tests you might get some errors with functions not being defined, especially when first adding a new spec file for a component. This is because there are functions on services that the component is trying to call that are not defined on the mocked services within testing-utils.ts. You just need to add them to the mocked services and just return some dummy test data. Note that before each unit test the code in the constructor and `ngOnInit()` is called.

#### Problems With The Expected Result Changing

Be careful when using reusable test data which has an `Object` type or contains something with an `Object` type. `Object`s get copied by reference instead of by value in assignment statements. 

This means that when you initialize a field to an `Object` and then run that test it will change the actual test data. If that test data is then used in later unit tests, those tests will be expecting something different. 

``` ts
const testData = { key: 'value' };
...
it('should not change the resuable test data in assignments', () => { 
    component.data = testData;
    ...
});
...
it('should not change the resuable test data in functions', () => { 
    component.changeData(testData);
    ...
});
```

You can avoid this by using the function `cloneDeep()` from the library `lodash` on the test data you assign to components or pass into functions. This makes a deep copy of the object instead of copying its reference.

``` ts
import cloneDeep from 'lodash/cloneDeep';
const testData = { key: 'value' };
...
it('should not change the resuable test data in assignments', () => { 
    component.data = cloneDeep(testData);
    ...
});
...
it('should not change the resuable test data in functions', () => { 
    component.changeData(cloneDeep(testData));
    ...
});
```

#### DebugElement, NativeElement and ComponentInstance

debugElement returns 
nativeElement returns 
componentInstance returns an object
querySelectorAll returns a NodeList

Different ways of grabbing elements

debugElement.nativeElement.querySelector()
debugElement.nativeElement.querySelectorAll()
debugElement.nativeElement.querySelectorAll().nativeElement
debugElement.query(By.css())
debugElement.query(By.css()).nativeElement
debugElement.query(By.css()).componentInstance

### Writing Integration Tests
---

In unit tests we replace all the child components and services with simple mocked versions. In Integration tests we include the actual child components. We write integration tests in a separate file. There are automated integration tests written by testers so developers shouldn’t have to write many of these.

There are many different integration tests we could create. We should make sure to agree on what to write integration tests for before writing any. We usually only test the most important and complex components that may have a lot of child components or experience bugs in the past.
<br />
<br />
<br />
