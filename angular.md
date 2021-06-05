# Angular Notes

* To create a new component,

    `ng generate component searchbox --skipTests=true --module app`
* create component in a specific folder
    `ng g c --module app  templates\card-image`


### Agular forms and dialog with angular material

```ts
//FormsDialog
export class UserFormDialogComponent implements OnInit {

  userForm: FormGroup;

  constructor(
    private fb: FormBuilder,
    private dialogRef: MatDialogRef<UserFormDialogComponent>,
    @Inject(MAT_DIALOG_DATA) public data: User) {

    this.userForm = this.fb.group({
      firstName: new FormControl(this.data?.firstName),
      lastName: new FormControl(this.data?.lastName),
      email: new FormControl(this.data?.email),
    });
    
  }

  ngOnInit(): void {

  }

  Save() {
    console.debug(this.userForm.value);
    this.dialogRef.close(this.userForm.value);
  }

}
```

```html
<table mat-table [dataSource]="dataSource" class="mat-elevation-z8">
    <ng-container matColumnDef="firstName">
        <th mat-header-cell *matHeaderCellDef> First Name </th>
        <td mat-cell *matCellDef="let user"> {{user.firstName}} </td>
    </ng-container>

    <ng-container matColumnDef="lastName">
        <th mat-header-cell *matHeaderCellDef> Last Name </th>
        <td mat-cell *matCellDef="let user"> {{user.lastName}} </td>
    </ng-container>

    <ng-container matColumnDef="email">
        <th mat-header-cell *matHeaderCellDef> Email </th>
        <td mat-cell *matCellDef="let user"> {{user.email}} </td>
    </ng-container>

    <ng-container matColumnDef="edit">
        <th mat-header-cell *matHeaderCellDef></th>
        <td mat-cell *matCellDef="let user">
            <button mat-icon-button (click)="editUser(user)"><mat-icon matTooltip="edit">edit</mat-icon></button>
        </td>                
    </ng-container>

    <tr mat-header-row *matHeaderRowDef="displayColumns"></tr>
    <tr mat-row *matRowDef="let row; columns: displayColumns;"></tr>
</table>
```

opening the dialog from another component

```ts
OpenUserForm(user: User): void {

  const dailogConfig = new MatDialogConfig();

  dailogConfig.data = user;

  const dialogRef = this.dialog.open(UserFormDialogComponent, dailogConfig);

  dialogRef.afterClosed().subscribe(result => {
    console.debug(result);
    this.updatedUser = result;
  });
}

editUser(user: User) {
  console.debug(user);
  this.OpenUserForm(user);
}
```

### using mat-select and assigning objects as value use `comparewith` property to selected value

```html
 <ng-container matColumnDef="group">
  <th mat-header-cell *matHeaderCellDef> Group </th>
  <td mat-cell *matCellDef="let user;">{{user.group}}
    <mat-select multiple [(value)]="user.group" [compareWith]="compareUserGroups">
      <mat-option *ngFor="let g of allGroups" [value]="g" [disabled]="true">
        {{ g.name }}
      </mat-option>
    </mat-select>
  </td>
</ng-container>
``` 

### nested parent child api calls using rxjs

- map - iterates thorugh the observable collection and transforms to another observable collection 
- mergeMap - collects emitted values from its inner observables and emits to its subscribers
- [Reference](https://www.tektutorialshub.com/angular/using-mergemap-in-angular/)

```ts
this.wfService.getAllUsers()
  .pipe(
    switchMap(users => from(users)),
    mergeMap(user =>
      this.wfService.getAllGroupsOfUser(user)
        .pipe(
          tap(userGroups => user.groups = userGroups),
          map(() => user)
        )
    ),
    toArray(),
    map(users => users)
  )
  .subscribe(users => this.bindData(users))
```

- using forkjoin to merge to api calls

```ts
let updateUser = this.wfService.updateUser(user);
let updateUserGroup = this.wfService.addUserToGroups(user, user.groups.map(g => g.name));
forkJoin({ updateUser, updateUserGroup }).subscribe(forkResult => {
  console.debug(forkResult);
  this.refreshDataSource();
});
```

### adding a control dynamically

```html
<button (click)="addCondition()">Add Condition</button>
<div formArrayName="conditions">
  <div *ngFor="let condition of conditions.controls; let i=index">
    <label>
      Condition {{i}}: <input [formControlName]="i" />
    </label>
  </div>
</div>
```
```ts
emailReviewForm: FormGroup;

constructor(private fb: FormBuilder) {
  this.emailReviewForm = this.fb.group({
    trigger: [''],
    inputModel: [''],
    conditions: this.fb.array([this.fb.control('')]),
  })
}

get conditions() {
  return this.emailReviewForm.get('conditions') as FormArray;
}

addCondition() {
  this.conditions.push(new FormControl(''));
}
```