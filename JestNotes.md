### inject service to test template:
```
  it('testing... , 
    inject([Service], (service) => {
```
* No `() => ` before the inject
* Service should be exists in the Module
The same as 
` const service - TestBed.get(Service)`
