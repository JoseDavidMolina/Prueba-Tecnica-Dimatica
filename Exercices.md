# NodeJs

## Exercice: Is there a problem? _(1 points)_

```javascript
// Call web service and return count user, (got is library to call url)
async function getCountUsers() {
    try {
      const response = await got.get('https://my-webservice.moveecar.com/users/count');
      const data = JSON.parse(response.body);
      return { total: data.count };
  } catch (error) {
      throw new Error('Error fetching user count: ' + error.message);
  }
}

// Add total from service with 20
async function computeResult() {
    const result = await getCountUsers();
    return result.total + 20;
}
```

## Exercice: Is there a problem? _(2 points)_

```javascript
// Call web service and return total vehicles, (got is library to call url)
async function getTotalVehicles() {
    try {
        const response = await got.get('https://my-webservice.moveecar.com/vehicles/total');
        return parseInt(response.body);
    } catch (error) {
        throw new Error('Error fetching total vehicles: ' + error.message);
    }
}

function getPlurial() {
    const total = await getTotalVehicles();
    if (total <= 0) {
        return 'none';
    }
    if (total <= 10) {
        return 'few';
    }
    return 'many';
}
```

## Exercice: Unit test _(2 points)_

Write unit tests in jest for the function below in typescript

```typescript
import { expect, test } from '@jest/globals';

function getCapitalizeFirstWord(name: string): string {
  if (name == null) {
    throw new Error('Failed to capitalize first word with null');
  }
  if (!name) {
    return name;
  }
  return name.split(' ').map(
    n => n.length > 1 ? (n.substring(0, 1).toUpperCase() + n.substring(1).toLowerCase()) : n
  ).join(' ');
}

test('Capitalize first word of a single word', () => {
  const result = getCapitalizeFirstWord('hello');
  expect(result).toBe('Hello');
});

test('Capitalize first word of multiple words', () => {
  const result = getCapitalizeFirstWord('hello world');
  expect(result).toBe('Hello World');
});

test('Return empty string for empty input', () => {
  const result = getCapitalizeFirstWord('');
  expect(result).toBe('');
});

test('Return same word for one-letter input', () => {
  const result = getCapitalizeFirstWord('a');
  expect(result).toBe('A');
});

test('Return same word for already capitalized word', () => {
  const result = getCapitalizeFirstWord('Hello');
  expect(result).toBe('Hello');
});

test('Throw error for null input', () => {
  expect(() => {
    getCapitalizeFirstWord(null);
  }).toThrowError('Failed to capitalize first word with null');
});
```

# Angular

## Exercice: Is there a problem and improve the code _(5 points)_

```typescript
import { Component, OnInit } from '@angular/core';
import { Subject, concat, of, timer } from 'rxjs';
import { concatMap } from 'rxjs/operators';
import { UserService } from './user.service';

@Component({
  selector: 'app-users',
  template: `
    <input type="text" [(ngModel)]="query" (ngModelChange)="querySubject.next($event)">
    <div *ngFor="let user of users">
      {{ user.email }}
    </div>
  `
})
export class AppUsers implements OnInit {
  query = '';
  querySubject = new Subject<string>();
  users: { email: string }[] = [];

  constructor(private userService: UserService) {}

  ngOnInit(): void {
    this.querySubject.pipe(
      concatMap(q =>
        timer(0, 60000).pipe(
          concatMap(() => this.userService.findUsers(q))
        )
      )
    ).subscribe({
      next: (res) => this.users = res
    });
  }
}
```

## Exercice: Improve performance _(5 points)_

```typescript
@Component({
  selector: 'app-users',
  template: `
    <div *ngFor="let user of users">
        {{ user.capitalizedName }}
    </div>
  `
})
export class AppUsers {

  @Input()
  set users(value: { name: string; }[]) {
    this.processUsers(value);
  }

  processedUsers: { capitalizedName: string; }[] = [];

  constructor() {}

  private processUsers(users: { name: string; }[]): void {
    this.processedUsers = users.map(user => ({
      capitalizedName: this.getCapitalizeFirstWord(user.name)
    }));
  }
  
  private getCapitalizeFirstWord(name: string): string {
    return name.split(' ').map(n => n.substring(0, 1).toUpperCase() + n.substring(1).toLowerCase()).join(' ');
  }
}
```

## Exercice: Forms _(8 points)_

Complete and modify `AppUserForm` class to use Angular Reactive Forms. Add a button to submit.

The form should return data in this format

```typescript
{
  email: string; // mandatory, must be a email
  name: string; // mandatory, max 128 characters
  birthday?: Date; // Not mandatory, must be less than today
  address: { // mandatory
    zip: number; // mandatory
    city: string; // mandatory, must contains only alpha uppercase and lower and space
  };
}
```

```typescript
import { Component, Output, EventEmitter } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

@Component({
  selector: 'app-user-form',
  template: `
    <form [formGroup]="userForm" (ngSubmit)="onSubmit()">
      <input type="text" placeholder="email" formControlName="email">
      <input type="text" placeholder="name" formControlName="name">
      <input type="date" placeholder="birthday" formControlName="birthday">
      <input type="number" placeholder="zip" formControlName="zip">
      <input type="text" placeholder="city" formControlName="city">
      <button type="submit" [disabled]="userForm.invalid">Submit</button>
    </form>
  `
})
export class AppUserForm {
  @Output() event = new EventEmitter<any>();

  userForm: FormGroup;

  constructor(private formBuilder: FormBuilder) {
    this.userForm = this.formBuilder.group({
      email: ['', [Validators.required, Validators.email]],
      name: ['', [Validators.required, Validators.maxLength(128)]],
      birthday: [null, [this.dateValidator]],
      zip: ['', [Validators.required]],
      city: ['', [Validators.required, Validators.pattern(/^[a-zA-Z\s]+$/)]]
    });
  }

  dateValidator(control: any) {
    const selectedDate = new Date(control.value);
    const today = new Date();

    return selectedDate <= today ? null : { futureDate: true };
  }

  onSubmit(): void {
    if (this.userForm.valid) {
      const formData = this.userForm.value;
      this.event.emit(formData);
    }
  }
}
```

# CSS & Bootstrap

## Exercice: Card _(5 points)_

![image](uploads/0388377207d10f8732e1d64623a255b6/image.png)

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Bootstrap Exercise</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>

<body>

    <div class="container mt-4">
        <div class="row justify-content-center">
            <div class="col-md-4">
                <div class="card">
                    <div class="card-body">
                        <h5 class="card-title">Exercise</h5>
                        <p class="card-text">Redo this card in CSS and if possible using Bootstrap 4 or 5</p>
                        <div class="d-flex flex-row justify-content-end gap-2">
                            <div class="btn-group" role="group">
                                <button type="button" class="btn btn-dark">Got it</button>
                                <button type="button" class="btn btn-light border border-dark">I don't know</button>
                            </div>
                            <div class="dropdown">
                                <button class="btn btn-secondary dropdown-toggle" type="button" id="dropdownMenuButton"
                                    data-bs-toggle="dropdown" aria-expanded="false">
                                    Options
                                </button>
                                <ul class="dropdown-menu" aria-labelledby="dropdownMenuButton">
                                    <li><a class="dropdown-item" href="#">Option 1</a></li>
                                    <li><a class="dropdown-item" href="#">Option 2</a></li>
                                    <li><a class="dropdown-item" href="#">Option 3</a></li>
                                </ul>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>

</html>
```
# MongoDb

## Exercice: MongoDb request _(3 points)_

MongoDb collection `users` with schema

``` typescript
  {
    email: string;
    first_name: string;
    last_name: string;
    roles: string[];
    last_connection_date: Date;
  }
```

Complete the query, you have a variable that contains a piece of text to search for. Search by exact email, starts with first or last name and only users logged in for 6 months

``` typescript
const text = "example@email.com"; 

const date = new Date();
date.setMonth(date.getMonth() - 6);

db.collections('users').find({
    $and: [
        {
            $or: [
                { email: searchText },
                { first_name: { $regex: `^${text}`, $options: 'i' } },
                { last_name: { $regex: `^${text}`, $options: 'i' } }
            ]
        },
        { last_connection_date: { $gte: date } }
    ]
});
```

What should be added to the collection so that the query is not slow?

## Exercice: MongoDb aggregate _(5 points)_

MongoDb collection `users` with schema

``` typescript
  {
    email: string;
    first_name: string;
    last_name: string;
    roles: string[];
    last_connection_date: Date;
  }
```

Complete the aggregation so that it sends user emails by role ({_id: 'role', users: [email,...]})

``` typescript
db.collections('users').aggregate([
    {
        $group: {
            _id: "$roles",
            users: { $push: "$email" }
        }
    },
    {
        $unwind: "$_id"
    },
    {
        $project: {
            _id: 0,
            role: "$_id",
            users: 1
        }
    }
]);
```

## Exercice: MongoDb update _(5 points)_

MongoDb collection `users` with schema

``` typescript
  {
    email: string;
    first_name: string;
    last_name: string;
    roles: string[];
    last_connection_date: Date;
    addresses: {
        zip: number;
        city: string;
    }[]:
  }
```

Update document `ObjectId("5cd96d3ed5d3e20029627d4a")`, modify only `last_connection_date` with current date

``` typescript
db.collections('users').updateOne(
  { _id: ObjectId("5cd96d3ed5d3e20029627d4a") },
  { $set: { last_connection_date: currentDate } }
);;
```

Update document `ObjectId("5cd96d3ed5d3e20029627d4a")`, add a role `admin`

``` typescript
db.collections('users').updateOne(
  { _id: ObjectId("5cd96d3ed5d3e20029627d4a") },
  { $set: { role: "admin" } }
);
```

Update document `ObjectId("5cd96d3ed5d3e20029627d4a")`, modify addresses with zip `75001` and replace city with `Paris 1`

``` typescript
db.collections('users').updateOne(
  { 
    _id: ObjectId("5cd96d3ed5d3e20029627d4a"),
    "addresses.zip": "75001"
  },
  { 
    $set: { "addresses.$.city": "Paris 1" }
  }
);
```