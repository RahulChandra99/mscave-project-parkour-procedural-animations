<h2 align="center">Climbing and Parkour Mechanics (Zelda / Uncharted Style)</h2>
<p align="center">Built using Unreal Engine 5, C++ and Control Rig</p>

---

## Table of Contents

- [Gameplay Demo](#gameplay-demo)
- [Project Overview](#project-overview)
- [Project Details](#project-details)
- [System Highlights and Code Implementation](#system-highlights-and-code-implementation)
  - [Surface Detection System](#surface-detection-system)
  - [Custom Climbing Movement Mode](#custom-climbing-movement-mode)
  - [Surface Aligned Movement](#surface-aligned-movement)
  - [Climbing Animation System](#climbing-animation-system)
  - [Ledge Detection and Vault System](#ledge-detection-and-vault-system)
  - [Control Rig IK System](#control-rig-ik-system)
  - [Motion Warping System](#motion-warping-system)
- [Architecture](#architecture)
- [Performance / Optimisation](#performance--optimisation)


## Gameplay Demo

<p align="center">
  <b>Click the image to open the video in a new tab.</b>
</p>

<p align="center">
  <a href="https://www.youtube.com/watch?v=07IdJnzLCgQ" target="_blank">
    <img src="https://github.com/user-attachments/assets/e5fa67ed-c5ca-4784-8291-d3973c81fbbd" width="520"/>
  </a>
</p>

---

## Project Overview

This project implements a dynamic climbing and vaulting system inspired by AAA traversal mechanics.

The system allows the character to:

- detect climbable surfaces dynamically
- switch to a custom climbing movement mode
- align movement with surface normals
- perform vault or climb actions based on obstacle height
- use IK for hand and foot placement
- adapt animations using motion warping

The focus is procedural traversal, environment interaction, and animation driven movement.

---

## Project Details

| Platform | Engine | Duration | Team Size | Role |
|---|---|---|---|---|
| PC | Unreal Engine 5 | University Project | Solo | Gameplay Programmer |

---

# System Highlights and Code Implementation

---

## Surface Detection System

### What this does
Detects climbable surfaces using capsule and line traces.  
Collects surface normal, distance, and angle used by climbing movement.

### View code

<details>
<summary><b>Capsule trace for climbable surfaces</b></summary>

```cpp
bool UClimbingComponent::TraceClimbableSurfaces(TArray<FHitResult>& OutHits) const
{
    FVector Start = Owner->GetActorLocation();
    FVector End = Start + Owner->GetActorForwardVector() * TraceDistance;

    FCollisionShape Capsule = FCollisionShape::MakeCapsule(Radius, HalfHeight);

    return GetWorld()->SweepMultiByObjectType(
        OutHits,
        Start,
        End,
        FQuat::Identity,
        ClimbableObjectTypes,
        Capsule
    );
}
```

</details>

---

## Custom Climbing Movement Mode

### What this does
Switches character into a custom movement mode that controls gravity, friction, and wall attachment.

### View code

<details>
<summary><b>Custom movement mode</b></summary>

```cpp
void UCustomMovementComponent::StartClimbing()
{
    SetMovementMode(MOVE_Custom, CustomMovementMode_Climb);
    Velocity = FVector::ZeroVector;
}

void UCustomMovementComponent::StopClimbing()
{
    SetMovementMode(MOVE_Walking);
}
```

</details>

---

## Surface Aligned Movement

### What this does
Aligns character rotation and velocity to match wall orientation.

### View code

<details>
<summary><b>Align rotation to surface</b></summary>

```cpp
FRotator UClimbingComponent::GetClimbRotation(const FVector& SurfaceNormal) const
{
    FVector Forward = -SurfaceNormal;
    return Forward.Rotation();
}
```

</details>

---

## Climbing Animation System

### What this does
Custom animation instance reads climb state and drives animation transitions.

### View code

<details>
<summary><b>Animation update</b></summary>

```cpp
void UClimbAnimInstance::NativeUpdateAnimation(float DeltaTime)
{
    if (!OwningCharacter) return;

    bIsClimbing = OwningCharacter->IsClimbing();
    ClimbSpeed = OwningCharacter->GetVelocity().Size();
}
```

</details>

---

## Ledge Detection and Vault System

### What this does
Detects ledges and triggers climb up or vault animation based on height and distance.

### View code

<details>
<summary><b>Ledge detection</b></summary>

```cpp
bool UClimbingComponent::HasReachedLedge() const
{
    FHitResult Hit;

    FVector Start = Owner->GetActorLocation() + FVector(0,0,LedgeHeight);
    FVector End = Start - FVector(0,0,TraceDepth);

    return GetWorld()->LineTraceSingleByChannel(Hit, Start, End, ECC_Visibility);
}
```

</details>

---

## Control Rig IK System

### What this does
Positions hands and feet using surface hit locations for accurate limb placement.

Add Control Rig graph screenshots here.

---

## Motion Warping System

### What this does
Adjusts vault animations dynamically to match obstacle height.

### View code

<details>
<summary><b>Motion warping target setup</b></summary>

```cpp
void AMyCharacter::SetVaultWarpTarget(FVector TargetLocation)
{
    FMotionWarpingTarget WarpTarget;
    WarpTarget.Name = "VaultTarget";
    WarpTarget.Location = TargetLocation;

    MotionWarpingComponent->AddOrUpdateWarpTarget(WarpTarget);
}
```

</details>

---

# Architecture

- Custom movement component for climbing mode
- Surface detection using traces
- Animation driven locomotion
- Control Rig IK placement
- Motion warping based transitions
- Data driven environment interaction

---

# Performance / Optimisation

- Custom movement mode avoids unnecessary physics updates
- Trace calls limited to interaction range
- Lightweight IK target updates
- Animation state driven updates

---

