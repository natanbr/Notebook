https://angular.io/guide/testing#testing

- [Service testing](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#service-testing)
  - [Outside angular](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#outside-angular)
  - [Using spy service](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#using-spy-service)
- [Angular TestBed](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#angular-testbed)
  - [Testing (services) without beforeEach()](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#testing-services-without-beforeeach)
  - [Testing HTTP services](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#testing-http-services)
- [Component Test](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#component-test)
  - [@Inputs()](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#inputs)
  - [@Outputs()](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#outputs)
  - [Component DOM testing](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#component-dom-testing)
    - [querySelector](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#querySelector)
    - [By.css()](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#bycss)
  - [change detection](https://github.com/natanbr/Notebook/wiki/change-detection)
    - [HTMLInutElement (input) value dispatchEvent()]()
  - [Component with a dependency](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#component-with-a-dependency)
  - [Component with async service](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#component-with-async-service)
    - [testing with spy](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#testing-with-spy)
    - [testing with fakeAsync()](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#testing-with-fakeasync)
    - [Async observables](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#async-observables)
    - [Async test with async()](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#async-test-with-async)
    - [Jasmine done()](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#jasmine-done)
    - [Component marble tests](https://github.com/natanbr/Notebook/blob/master/frontend-notebook/unit-testing/angular.io-notes.md#component-marble-tests)
    - 
    - 

# Service testing
### Outside angular
```
// Straight Jasmine testing without Angular's testing support
describe('ValueService', () => {
  let service: ValueService;
  beforeEach(() => { service = new ValueService(); });
```
### Using spy service
> Prefer spies as they are usually the easiest way to mock services
```
it('#getValue should return stubbed value from a spy', () => {
    // create `getValue` spy on an object representing the ValueService
    const valueServiceSpy =
      jasmine.createSpyObj('ValueService', ['getValue']);
 
    // set the value to return when the `getValue` spy is called.
    const stubValue = 'stub value';
    valueServiceSpy.getValue.and.returnValue(stubValue);
 
    masterService = new MasterService(valueServiceSpy);
```

## Angular TestBed
```
let masterService: MasterService;
let valueServiceSpy: jasmine.SpyObj<ValueService>;

beforeEach(() => {
  const spy = jasmine.createSpyObj('ValueService', ['getValue']);

  TestBed.configureTestingModule({
    // Provide both the service-to-test and its (spy) dependency
    providers: [
      MasterService,
      { provide: ValueService, useValue: spy }
    ]
  });
  // Inject both the service-to-test and its (spy) dependency
  masterService = TestBed.get(MasterService);
  valueServiceSpy = TestBed.get(ValueService);
});
```
### Testing (services) without beforeEach()
[Testing without before each](https://angular.io/guide/testing#testing-without-beforeeach)

### Testing HTTP services
[docs](https://angular.io/guide/testing#testing-http-services)
```
let httpClientSpy: { get: jasmine.Spy };
let heroService: HeroService;
 
beforeEach(() => {
  httpClientSpy = jasmine.createSpyObj('HttpClient', ['get']);
  heroService = new HeroService(<any> httpClientSpy);
```
**for returning async data as sync function:**
[asyncData](https://angular.io/guide/testing#async-observables) - Simulate delayed observable values with the `asyncData()` helper (a utility function that you'll have to [write yourself](a utility function that you'll have to write yourself))

```
it('should return expected heroes (HttpClient called once)', () => {
  const expectedHeroes: Hero[] =
    [{ id: 1, name: 'A' }, { id: 2, name: 'B' }];
 
  httpClientSpy.get.and.returnValue(asyncData(expectedHeroes));
 
  heroService.getHeroes().subscribe(
    heroes => expect(heroes).toEqual(expectedHeroes, 'expected heroes')
  );
  expect(httpClientSpy.get.calls.count()).toBe(1, 'one call');
});
```

# Component Test
without testing the Dom
```
const comp = new LightswitchComponent();
comp.click()
```

### @Inputs()
```
component.input = { ... }
```
> ⭐️ ...remembering to call the lifecycle hook methods as Angular does when running the app.
```
it('should welcome logged in user after Angular calls ngOnInit', () => {
  comp.ngOnInit();
  expect(comp.welcome).toContain(userService.user.name);
});
```

###  @Outputs()
```
it('raises the selected event when clicked', () => {
   comp.selected.subscribe(selectedHero => expect(selectedHero).toBe(hero));
```
## [Component DOM testing](https://angular.io/guide/testing#component-dom-testing)
> The tests in this guide are designed to run in a browser so a **nativeElement** value will always be an `HTMLElement`
### querySelector:
```
const bannerElement: HTMLElement = fixture.nativeElement;
const p = bannerElement.querySelector('p');
expect(p.textContent).toEqual('banner works!');
```
### By.css():
```
const bannerDe: DebugElement = fixture.debugElement;
const paragraphDe = bannerDe.query(By.css('p'));
const p: HTMLElement = paragraphDe.nativeElement;
expect(p.textContent).toEqual('banner works!');
```
## change detection
`fixture.detectChanges();`
automatic:
```
TestBed.configureTestingModule({
  declarations: [ BannerComponent ],
  providers: [
    { provide: ComponentFixtureAutoDetect, useValue: true }
  ]
});
```
> ComponentFixtureAutoDetect service responds to asynchronous activities such as promise resolution, timers, and DOM events

### [HTMLInutElement (input) value dispatchEvent()](https://angular.io/guide/testing#change-an-input-value-with-dispatchevent)
```
it('should convert hero name to Title Case', () => {
  // get the name's input and display elements from the DOM
  const hostElement = fixture.nativeElement;
  const nameInput: HTMLInputElement = hostElement.querySelector('input');
  const nameDisplay: HTMLElement = hostElement.querySelector('span');
 
  // simulate user entering a new name into the input box
  nameInput.value = 'quick BROWN  fOx';
 
  // dispatch a DOM event so that Angular learns of input value change.
  nameInput.dispatchEvent(newEvent('input'));
 
  // Tell Angular to update the display binding through the title pipe
  fixture.detectChanges();
 
  expect(nameDisplay.textContent).toBe('Quick Brown  Fox');
});
```
## Component with a dependency
```
providers:    [ {provide: UserService, useValue: userServiceStub } ]
```
**create service double**
```
let userServiceStub: Partial<UserService>;

userServiceStub = {
  isLoggedIn: true,
  user: { name: 'Test User'}
};
```
**Get injected services**
2 options:
```
// UserService actually injected into the component
userService = fixture.debugElement.injector.get(UserService);

// UserService from the root injector
userService = TestBed.get(UserService);
```
> Do not reference the userServiceStub object that's provided to the testing module in the body of your test. It does not work! The userService instance injected into the component is a completely different object, a clone of the provided userServiceStub. ❓ 

## Component with async service
### testing with spy
**synchronous** observable:
```
// Create a fake TwainService object with a `getQuote()` spy
const twainService = jasmine.createSpyObj('TwainService', ['getQuote']);
// Make the spy return a synchronous Observable with the test data
getQuoteSpy = twainService.getQuote.and.returnValue( of(testQuote) );
...
it('...', () => {
  expect(getQuoteSpy.calls.any()).toBe(true, 'getQuote called');
```
### testing with fakeAsync()
> when component method calls setTimeout() test must wait at least one full turn of the JavaScript engine before the value becomes available
```
it('....', fakeAsync(() => {
  // tell spy to return an error observable
  getQuoteSpy.and.returnValue(throwError('...'));
 
  fixture.detectChanges(); // onInit()
  // sync spy errors immediately after init
 
  tick(); // flush the component's setTimeout()
 
  fixture.detectChanges(); // update errorMessage within setTimeout()
 
  expect(errorMessage()).toMatch(/test failure/, 'should display error');
  expect(quoteEl.textContent).toBe('...', 'should show placeholder');
}));
```
### **Async** observables
**asyncData** and **asyncError** are [Async observable helpers](https://angular.io/guide/testing#async-observable-helpers)
```
// Simulate delayed observable values with the `asyncData()` helper
getQuoteSpy.and.returnValue(asyncData(testQuote));
...
it('should show quote after getQuote (fakeAsync)', fakeAsync(() => {
  fixture.detectChanges(); // ngOnInit()
  expect(quoteEl.textContent).toBe('...', 'should show placeholder');

  tick(); // flush the observable to get the quote
  fixture.detectChanges(); // update view

  expect(quoteEl.textContent).toBe(testQuote, 'should show quote');
  expect(errorMessage()).toBeNull('should not show error');
}));
```
### Async test with async()
> XHR calls within a test are rare so you can generally stick with fakeAsync(). But if you ever do need to call XHR, you'll want to know about async().
> When using an intervalTimer() such as setInterval() in async(), remember to cancel the timer with clearInterval() after the test, otherwise the async() never ends.
> Instead of calling tick(), it calls fixture.whenStable().

### Jasmine done()
> You can't call done() in async() or fakeAsync() 
```
it('should show last quote (quote done)', (done: DoneFn) => {
  fixture.detectChanges();

  component.quote.pipe( last() ).subscribe(() => {
    fixture.detectChanges(); // update view with quote
    expect(quoteEl.textContent).toBe(testQuote);
    done();
  });
});
```
### Component marble tests
> Start by installing the jasmine-marbles npm package
`const q$ = cold('---x|', { x: testQuote });`
[Testing RxJS Code with Marble Diagrams](https://github.com/ReactiveX/rxjs/blob/master/docs_app/content/guide/testing/marble-testing.md)