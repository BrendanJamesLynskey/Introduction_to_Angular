# Introduction to Angular

**The Full-Featured TypeScript Framework**

TypeScript | components | signals | DI | RxJS | CLI

---

## Table of Contents

1. Title
2. Agenda
3. What Is Angular?
4. Angular Architecture
5. The Angular CLI
6. Components & Templates
7. Data Binding
8. Directives
9. Signals & Reactive State
10. Services & Dependency Injection
11. RxJS & Observables
12. Routing & Navigation
13. Forms
14. HTTP Client
15. Pipes
16. Component Communication
17. Standalone Components
18. Testing
19. Performance & Build
20. Summary & Next Steps

---

## Slide 02 — Agenda

### Foundations
- What Is Angular?
- Architecture Overview
- The Angular CLI
- Components & Templates
- Data Binding

### Core Concepts
- Directives
- Signals & Reactive State
- Services & Dependency Injection
- RxJS & Observables
- Routing & Navigation

### Practical Patterns
- Forms (Template & Reactive)
- HTTP Client
- Pipes
- Component Communication
- Standalone Components

### Production
- Testing Strategies
- Performance & Build
- Summary & Next Steps

---

## Slide 03 — What Is Angular?

Angular is a **full-featured, opinionated TypeScript framework** maintained by Google for building single-page applications and progressive web apps.

### Timeline

| Year | Milestone |
|------|-----------|
| 2010 | AngularJS (v1.x) released by Google |
| 2016 | Angular 2 — complete rewrite in TypeScript |
| 2017-2022 | Angular 4-14 — Ivy renderer, strict mode |
| 2023 | Angular 16-17 — signals, new control flow, esbuild |
| 2024 | Angular 18-19 — zoneless, stable signals |

### Key Properties
- **TypeScript-first** — types built in
- **Batteries included** — router, forms, HTTP, testing
- **Dependency Injection** — hierarchical IoC
- **Reactive** — RxJS + Signals
- **CLI-driven** — scaffolding & build in one tool

Angular is *not* AngularJS. The modern framework (v2+) is a complete rewrite with a fundamentally different architecture.

---

## Slide 04 — Angular Architecture

Angular applications are built from five main building blocks:

- **Modules (NgModule)** — Organise related code into cohesive blocks. Standalone components are replacing NgModules in modern Angular.
- **Components** — Building blocks of the UI. Each has a TypeScript class, an HTML template, and optional CSS styles.
- **Services & DI** — Encapsulate business logic and data access. Injected via Angular's hierarchical injector system.
- **Directives** — Modify the behaviour or appearance of DOM elements.
- **Pipes** — Transform displayed values in templates.

The Angular runtime platform provides: Dependency Injection, Change Detection, Renderer, and Zone.js / Zoneless support.

---

## Slide 05 — The Angular CLI

The `@angular/cli` is the official tool for creating, developing, scaffolding, and building Angular projects.

```bash
# Install the CLI globally
npm install -g @angular/cli

# Create a new project
ng new my-app --style=scss --routing

# Serve with live reload
ng serve --open

# Generate artifacts
ng generate component features/dashboard
ng generate service core/auth
ng generate pipe shared/truncate
ng generate guard auth/role
```

| Command | Purpose |
|---------|---------|
| `ng new` | Scaffold a full project |
| `ng serve` | Dev server with HMR |
| `ng generate` | Create components, services, etc. |
| `ng build` | Production build (AOT + tree-shake) |
| `ng test` | Run unit tests (Karma/Jest) |
| `ng lint` | Lint with ESLint |
| `ng update` | Update Angular + run migrations |
| `ng add` | Add libraries with schematics |

---

## Slide 06 — Components & Templates

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-hero-card',
  standalone: true,
  template: `
    <div class="hero-card">
      <h2>{{ hero.name }}</h2>
      <p>Power: {{ hero.power }}</p>
      <button (click)="onSelect()">Select</button>
    </div>
  `,
  styles: [`
    .hero-card {
      border: 1px solid #ccc;
      border-radius: 8px;
      padding: 1rem;
    }
  `]
})
export class HeroCardComponent {
  hero = { name: 'Windstorm', power: 'Weather' };

  onSelect() {
    console.log(`Selected: ${this.hero.name}`);
  }
}
```

### @Component Decorator
- `selector` — custom HTML tag name
- `template / templateUrl` — inline or external HTML
- `styles / styleUrls` — scoped CSS
- `standalone` — no NgModule needed

### Template Syntax
- `{{ expr }}` — interpolation
- `[prop]="expr"` — property binding
- `(event)="handler()"` — event binding
- `[(ngModel)]="val"` — two-way binding

### Lifecycle Hooks
- `ngOnInit` — after first data binding
- `ngOnChanges` — when inputs change
- `ngOnDestroy` — cleanup subscriptions

---

## Slide 07 — Data Binding

Angular provides **four forms of data binding** that connect the component class to the template.

### Interpolation (one-way, class → view)
```html
<h1>Welcome, {{ user.name }}!</h1>
<p>Total: {{ getTotal() | currency }}</p>
```

### Property Binding (one-way, class → view)
```html
<img [src]="imageUrl" [alt]="imageAlt">
<button [disabled]="isLoading">Submit</button>
<div [class.active]="isActive"></div>
```

### Event Binding (one-way, view → class)
```html
<button (click)="save()">Save</button>
<input (keyup.enter)="search(term)">
<div (mouseover)="highlight($event)"></div>
```

### Two-Way Binding (class ↔ view)
```html
<!-- Requires FormsModule -->
<input [(ngModel)]="username">

<!-- Desugared equivalent -->
<input [ngModel]="username"
       (ngModelChange)="username = $event">
```

| Syntax | Direction | Example |
|--------|-----------|---------|
| `{{ }}` | Component → DOM | Text interpolation |
| `[ ]` | Component → DOM | Property / attribute / class / style |
| `( )` | DOM → Component | User events (click, keyup, etc.) |
| `[( )]` | Both | Form inputs with ngModel |

---

## Slide 08 — Directives

### Legacy Structural Directives

```html
<!-- *ngIf -->
<div *ngIf="user; else noUser">
  Hello, {{ user.name }}
</div>
<ng-template #noUser>
  <p>Please log in</p>
</ng-template>

<!-- *ngFor -->
<li *ngFor="let item of items; trackBy: trackById; let i = index">
  {{ i + 1 }}. {{ item.name }}
</li>

<!-- *ngSwitch -->
<div [ngSwitch]="role">
  <p *ngSwitchCase="'admin'">Admin</p>
  <p *ngSwitchDefault>User</p>
</div>
```

### New Built-in Control Flow (v17+)

```html
<!-- @if / @else -->
@if (user) {
  <p>Hello, {{ user.name }}</p>
} @else {
  <p>Please log in</p>
}

<!-- @for with required track -->
@for (item of items; track item.id) {
  <li>{{ item.name }}</li>
} @empty {
  <li>No items found</li>
}

<!-- @switch -->
@switch (role) {
  @case ('admin') { <p>Admin</p> }
  @default { <p>User</p> }
}
```

### Attribute Directives
- `ngClass` — conditional CSS classes
- `ngStyle` — conditional inline styles
- Custom: `@Directive({ selector: '[appHighlight]' })`

---

## Slide 09 — Signals & Reactive State

Angular **Signals** (stable in v17+) provide fine-grained, synchronous reactivity without Zone.js.

```typescript
import { signal, computed, effect } from '@angular/core';

@Component({
  selector: 'app-counter',
  standalone: true,
  template: `
    <p>Count: {{ count() }}</p>
    <p>Double: {{ double() }}</p>
    <button (click)="increment()">+1</button>
  `
})
export class CounterComponent {
  count = signal(0);
  double = computed(() => this.count() * 2);

  constructor() {
    effect(() => {
      console.log('Count changed:', this.count());
    });
  }

  increment() {
    this.count.update(c => c + 1);
  }
}
```

### signal()
Creates a writable reactive value. Read by calling it as a function. Mutate with `.set()`, `.update()`, or `.mutate()`.

### computed()
Derives a read-only signal from other signals. Lazily evaluated and memoized.

### effect()
Runs a side-effect whenever tracked signals change. Useful for logging, localStorage sync, or analytics.

### Signals vs RxJS
- Signals: synchronous, simple state
- RxJS: async streams, complex transformations
- `toSignal()` / `toObservable()` for interop

---

## Slide 10 — Services & Dependency Injection

```typescript
import { Injectable, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable({ providedIn: 'root' })
export class HeroService {
  private http = inject(HttpClient);
  private apiUrl = '/api/heroes';

  getAll() {
    return this.http.get<Hero[]>(this.apiUrl);
  }

  getById(id: number) {
    return this.http.get<Hero>(`${this.apiUrl}/${id}`);
  }

  create(hero: Partial<Hero>) {
    return this.http.post<Hero>(this.apiUrl, hero);
  }
}
```

### @Injectable
`providedIn: 'root'` registers a singleton at the root injector. Tree-shakeable — only included if actually injected.

### inject() function
Modern alternative to constructor injection. Can be used in components, directives, pipes, and services.

### Hierarchical Injectors
- **Root** — app-wide singletons
- **Module** — scoped to a lazy module
- **Component** — new instance per component
- **Element** — NodeInjector on the DOM tree

### InjectionToken
```typescript
export const API_URL = new InjectionToken<string>('API_URL');
{ provide: API_URL, useValue: '/api' }
url = inject(API_URL);
```

---

## Slide 11 — RxJS & Observables

```typescript
import { Subject, switchMap, debounceTime, distinctUntilChanged, catchError, of } from 'rxjs';

@Component({
  selector: 'app-search',
  standalone: true,
  imports: [AsyncPipe],
  template: `
    <input (input)="onSearch($event)">
    @for (hero of results$ | async; track hero.id) {
      <p>{{ hero.name }}</p>
    }
  `
})
export class SearchComponent {
  private search$ = new Subject<string>();
  private heroService = inject(HeroService);

  results$ = this.search$.pipe(
    debounceTime(300),
    distinctUntilChanged(),
    switchMap(term =>
      this.heroService.search(term).pipe(
        catchError(() => of([]))
      )
    )
  );

  onSearch(e: Event) {
    const val = (e.target as HTMLInputElement).value;
    this.search$.next(val);
  }
}
```

### Core Types
- **Observable** — lazy stream of values
- **Subject** — multicast, push values manually
- **BehaviorSubject** — holds current value
- **ReplaySubject** — replays N last values

### Key Operators

| Operator | Purpose |
|----------|---------|
| `map` | Transform emitted values |
| `filter` | Emit only matching values |
| `switchMap` | Cancel previous inner observable |
| `mergeMap` | Run inner observables concurrently |
| `combineLatest` | Combine latest from multiple streams |
| `takeUntilDestroyed` | Auto-unsubscribe on destroy |

### AsyncPipe
Subscribes in the template and auto-unsubscribes. Preferred over manual `.subscribe()`.

---

## Slide 12 — Routing & Navigation

```typescript
import { Routes } from '@angular/router';

export const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  { path: 'home', component: HomeComponent },
  {
    path: 'heroes',
    loadComponent: () =>
      import('./heroes/list.component').then(m => m.HeroListComponent),
  },
  {
    path: 'heroes/:id',
    loadComponent: () =>
      import('./heroes/detail.component').then(m => m.HeroDetailComponent),
    resolve: { hero: heroResolver },
    canActivate: [authGuard],
  },
  {
    path: 'admin',
    loadChildren: () =>
      import('./admin/admin.routes').then(m => m.ADMIN_ROUTES),
    canMatch: [adminGuard],
  },
  { path: '**', component: NotFoundComponent },
];
```

### Lazy Loading
`loadComponent` and `loadChildren` split code into separate chunks — loaded on demand for faster initial load.

### Functional Guards (v15+)
```typescript
export const authGuard: CanActivateFn = (route, state) => {
  const auth = inject(AuthService);
  return auth.isLoggedIn() ? true : inject(Router).createUrlTree(['/login']);
};
```

### Resolvers
```typescript
export const heroResolver: ResolveFn<Hero> = (route) => {
  const id = +route.paramMap.get('id')!;
  return inject(HeroService).getById(id);
};
```

---

## Slide 13 — Forms

Angular offers two approaches: **Template-driven** (simple, directive-based) and **Reactive** (explicit, code-based).

### Template-Driven
```html
<form #f="ngForm" (ngSubmit)="save(f)">
  <input name="name" ngModel required minlength="3" #name="ngModel">
  @if (name.invalid && name.touched) {
    <span class="error">Name is required (min 3 chars)</span>
  }
  <button [disabled]="f.invalid">Save</button>
</form>
```

Uses `FormsModule`. Good for simple forms with minimal validation.

### Reactive Forms
```typescript
@Component({ /* ... */ })
export class ProfileFormComponent {
  private fb = inject(FormBuilder);

  form = this.fb.group({
    name: ['', [Validators.required, Validators.minLength(3)]],
    email: ['', [Validators.required, Validators.email]],
    address: this.fb.group({
      street: [''],
      city: [''],
      zip: ['', Validators.pattern(/\d{5}/)]
    })
  });

  save() {
    if (this.form.valid) {
      console.log(this.form.getRawValue());
    }
  }
}
```

Uses `ReactiveFormsModule`. Typed, testable, dynamic.

---

## Slide 14 — HTTP Client

```typescript
import { provideHttpClient, withInterceptors } from '@angular/common/http';

export const appConfig = {
  providers: [
    provideHttpClient(withInterceptors([authInterceptor])),
  ]
};

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const token = inject(AuthService).getToken();
  if (token) {
    req = req.clone({
      setHeaders: { Authorization: `Bearer ${token}` }
    });
  }
  return next(req).pipe(
    catchError(err => {
      if (err.status === 401) {
        inject(Router).navigate(['/login']);
      }
      return throwError(() => err);
    })
  );
};
```

### provideHttpClient()
Standalone replacement for `HttpClientModule`. Supports functional interceptors and fetch backend.

### Typed Responses
```typescript
this.http.get<Hero[]>('/api/heroes').subscribe(heroes => {
  // heroes is Hero[]
});
```

### Error Handling
- `catchError` — handle per-request errors
- `retry(3)` — automatic retries
- Interceptors — global error handling
- `HttpErrorResponse` — typed error object

---

## Slide 15 — Pipes

Pipes transform displayed values in templates. Angular ships with many **built-in pipes** and supports **custom pipes**.

### Built-in Pipes

| Pipe | Example Output |
|------|---------------|
| `date` | `{{ d \| date:'mediumDate' }}` → Apr 6, 2026 |
| `currency` | `{{ 42.5 \| currency:'EUR' }}` → €42.50 |
| `uppercase` | `{{ 'hello' \| uppercase }}` → HELLO |
| `json` | Debug-prints object as JSON |
| `async` | Subscribes to Observable/Promise |
| `slice` | Subset of an array or string |
| `keyvalue` | Iterates over object entries |

### Custom Pipe
```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'truncate', standalone: true, pure: true })
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit = 50, ellipsis = '...'): string {
    if (!value) return '';
    return value.length > limit
      ? value.substring(0, limit) + ellipsis
      : value;
  }
}

// Usage: {{ longText | truncate:30:'...' }}
```

### Pure vs Impure
- **Pure pipes** — only re-evaluate when input *reference* changes. Default and performant.
- **Impure pipes** — re-evaluate on every change detection cycle. Use sparingly (`pure: false`).

---

## Slide 16 — Component Communication

### @Input / @Output
```typescript
@Component({
  selector: 'app-child',
  template: `
    <p>{{ title }}</p>
    <button (click)="notify.emit('hi')">Notify Parent</button>
  `
})
export class ChildComponent {
  @Input({ required: true }) title!: string;
  @Output() notify = new EventEmitter<string>();
}

// parent template
<app-child [title]="parentTitle" (notify)="onNotify($event)"></app-child>
```

### Signal Inputs (v17.1+)
```typescript
export class CardComponent {
  title = input.required<string>();
  subtitle = input('Default value');
  uppercaseTitle = computed(() => this.title().toUpperCase());
}
```

### ViewChild / ContentChild
```typescript
@ViewChild('chart') chartRef!: ElementRef;
@ViewChild(ChildComponent) child!: ChildComponent;
@ContentChild(HeaderDirective) header!: HeaderDirective;
```

### Content Projection
```html
<!-- card.component.html -->
<div class="card">
  <ng-content select="[header]"></ng-content>
  <ng-content></ng-content>
  <ng-content select="[footer]"></ng-content>
</div>

<!-- Usage -->
<app-card>
  <h2 header>Title</h2>
  <p>Body content here</p>
  <button footer>Action</button>
</app-card>
```

---

## Slide 17 — Standalone Components

Since Angular 14+, components can be **standalone** — no NgModule required. This is the **recommended default** in Angular 17+.

```typescript
@Component({
  selector: 'app-dashboard',
  standalone: true,
  imports: [CommonModule, RouterLink, HeroCardComponent, TruncatePipe],
  template: `
    @for (hero of heroes(); track hero.id) {
      <app-hero-card [hero]="hero" />
    }
    <a routerLink="/settings">Settings</a>
  `
})
export class DashboardComponent {
  heroes = signal<Hero[]>([]);
}

// bootstrap without NgModule (main.ts)
bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes),
    provideHttpClient(withInterceptors([authInterceptor])),
    provideAnimationsAsync(),
  ]
});
```

### Why Standalone?
- Simpler mental model — no NgModule boilerplate
- Better tree-shaking — imports are explicit
- Easier lazy loading — `loadComponent`
- Faster compilation — fewer files to process

### Migration from NgModules
- CLI schematic: `ng g @angular/core:standalone`
- Add `standalone: true` to components
- Move declarations to `imports` array
- Replace module providers with `provide*` functions

### importProvidersFrom()
```typescript
providers: [importProvidersFrom(SomeLibraryModule.forRoot())]
```

---

## Slide 18 — Testing

### Unit Testing with TestBed
```typescript
describe('HeroCardComponent', () => {
  let component: HeroCardComponent;
  let fixture: ComponentFixture<HeroCardComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [HeroCardComponent]
    }).compileComponents();

    fixture = TestBed.createComponent(HeroCardComponent);
    component = fixture.componentInstance;
  });

  it('should display hero name', () => {
    component.hero = { id: 1, name: 'Storm', power: 'Weather' };
    fixture.detectChanges();
    const el: HTMLElement = fixture.nativeElement;
    expect(el.querySelector('h2')?.textContent).toContain('Storm');
  });

  it('should emit on select', () => {
    spyOn(component.notify, 'emit');
    const btn = fixture.nativeElement.querySelector('button');
    btn.click();
    expect(component.notify.emit).toHaveBeenCalled();
  });
});
```

### Service Testing
```typescript
describe('HeroService', () => {
  let service: HeroService;
  let httpMock: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [provideHttpClient(), provideHttpClientTesting()]
    });
    service = TestBed.inject(HeroService);
    httpMock = TestBed.inject(HttpTestingController);
  });

  it('should fetch heroes', () => {
    service.getAll().subscribe(heroes => {
      expect(heroes.length).toBe(2);
    });
    const req = httpMock.expectOne('/api/heroes');
    req.flush([{ id: 1 }, { id: 2 }]);
  });
});
```

### Testing Stack

| Tool | Role |
|------|------|
| Jasmine | Test framework (default) |
| Karma | Test runner (legacy) |
| Jest | Modern alternative (v16+) |
| Web Test Runner | Official replacement for Karma |
| Cypress / Playwright | E2E testing |

---

## Slide 19 — Performance & Build

### AOT Compilation
- Compiles templates at build time
- Catches template errors early
- Smaller bundle — no compiler shipped
- Faster rendering — pre-compiled views
- Default since Angular 9

### Tree Shaking
- Removes unused code from bundles
- `providedIn: 'root'` enables tree-shaking for services
- Standalone imports improve tree-shaking
- Webpack & esbuild both support it

### Lazy Routes
- Split features into separate chunks
- `loadComponent` / `loadChildren`
- Preloading strategies available
- Reduces initial load time significantly

### esbuild + Vite (v17+)
```json
{
  "builder": "@angular-devkit/build-angular:application",
  "options": {
    "outputMode": "static",
    "ssr": false
  }
}
```
- Up to **87% faster** builds than Webpack
- HMR with Vite dev server
- Default in Angular 17+ new projects

### SSR with Angular Universal
```bash
ng add @angular/ssr
```
- Server-side rendering for SEO
- Hydration transfers state to client
- Incremental hydration (v18+)
- Pre-rendering static routes at build time

---

## Slide 20 — Summary & Next Steps

### What We Covered
- Angular architecture & CLI
- Components, templates, data binding
- Directives & new control flow
- Signals & reactive state
- Services & dependency injection
- RxJS & Observables
- Routing & lazy loading
- Template & Reactive forms
- HTTP client & interceptors
- Pipes (built-in & custom)
- Component communication
- Standalone components
- Testing with TestBed & Jest
- AOT & tree shaking
- esbuild + Vite
- SSR with Angular Universal

### Next Steps & Resources
- **angular.dev** — Official docs & tutorials
- **Angular Blog** — Release announcements
- **Angular Material** — Component library
- **NgRx** — State management with Redux pattern
- Build a CRUD app with routing & forms
- Add authentication with guards
- Implement state management (NgRx / signals)
- Deploy with SSR for production

**Thank you!** — Built with Reveal.js
