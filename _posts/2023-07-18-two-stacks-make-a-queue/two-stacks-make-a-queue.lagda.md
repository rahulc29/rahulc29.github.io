# Stacks and Queues

Stacks and queues are duals of each other. I wish to explore this duality from the perspective of algebra.

## Imports and Pragmas

```agda
{-# OPTIONS --cubical --guardedness #-}
open import Cubical.Core.Everything
open import Cubical.Foundations.Everything
open import Cubical.Data.Bool
open import Cubical.Data.List
open import Cubical.Data.Maybe
```

## Defining Stacks

We shall define stacks rather simply as a record that allows for

1. `push`
2. `pop`
3. `top`
4. `empty`

These should all follow the standard equations. Most important among these is the LIFO condition.

```agda
record Stack (X A : Set) : Set where
  field
    empty-stack : X → Bool
    push : X → A → X
    pop : X → Maybe X
    top : X → Maybe A
    pop-empty-nothing : ∀ (x : X) → empty-stack x ≡ true → pop x ≡ nothing
    top-empty-nothing : ∀ (x : X) → empty-stack x ≡ true → top x ≡ nothing
    pop-non-empty-something : ∀ (x : X) → empty-stack x ≡ false → Σ[ x' ∈ X ] (pop x ≡ just x')
    top-non-empty-something : ∀ (x : X) → empty-stack x ≡ false → Σ[ a ∈ A ] (top x ≡ just a)
    lifo : ∀ (x : X) → ∀ (a : A) → pop (push x a) ≡ just x
open Stack
```

Now we need a definition of queues.

```agda
record Queue (X A : Set) : Set where
  field
    empty-queue : X → Bool
    enqueue : X → A → X
    dequeue : X → Maybe X
    front : X → Maybe A
    dequeue-empty-nothing : ∀ (x : X) → empty-queue x ≡ true → dequeue x ≡ nothing
    front-empty-nothing : ∀ (x : X) → empty-queue x ≡ true → front x ≡ nothing
    dequeue-non-empty-something : ∀ (x : X) → empty-queue x ≡ false → Σ[ x' ∈ X ] (dequeue x ≡ just x')
    front-non-empty-something : ∀ (x : X) → empty-queue x ≡ false → Σ[ a ∈ A ] (front x ≡ just a)
    fifo-empty-front : ∀ (x₀ : X) → ∀ (a : A) → empty-queue x₀ ≡ true → front (enqueue x₀ a) ≡ just a
    fifo-non-empty-front : ∀ (x₀ : X) → ∀ (a : A) → empty-queue x₀ ≡ false → front (enqueue x₀ a) ≡ front x₀
    fifo-empty-dequeue : ∀ (x₀ : X) → ∀ (a : A) → empty-queue x₀ ≡ true → dequeue (enqueue x₀ a) ≡ just x₀
    fifo-non-empty-dequeue : ∀ (x₀ : X) → ∀ (a : A) → empty-queue x₀ ≡ false → dequeue (enqueue x₀ a) ≡ map-Maybe (λ x → enqueue x a) (dequeue x₀)
open Queue
```
