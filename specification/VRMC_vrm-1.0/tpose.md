# VRM T-pose: The specification of the rest pose in VRM

## Overview
VRM models must follow the specification of the rest pose in VRM.
This document describes the spec of the pose, and we call it "VRM T-pose".

## Definition
The VRM T-pose has two main criteria, all of which must be met.
First, it defines the criteria for the appearance of skinned meshes.
Second, it defines the criteria of the numerical values of node transforms.

Basically, the pose goes from a straight standing posture to a T-shaped pose which raises only the upper arm bones directly to the side.

### Definition based on the appearance of skinned meshes
This section defines the VRM T-pose by the appearance of the VRM model.

For example, it is important to be able to calculate the height of the sole surface for a VRM model.
If it can be correctly calculated, the VRM model can be properly grounded in virtual space.
Typically, the Foot bone of a humanoid model is located at the ankle.
Or, the height of the sole depends on the appearance of the mesh.
Therefore, it is impossible to calculate the height of the sole surface by simply seeing the bone transform.
The height information of the sole surface must be defined based on the appearance of the mesh.

All axes mentioned in the following definitions are in global space unless specified otherwise.

#### Definition 1.1. Standing straight toward the +Z axis, symmetrical on the X axis in appearance
The legs, torso, head, and eyes of a VRM model must be oriented along the +Z axis, symmetrical on the X axis, and standing straight.

"Standing straight" means a relaxed, and neutral posture for the VRM model.
It is symmetrical at X: 0.0, gazing straight ahead.
Seeing from the side, It should be as straight as possible from feet to head near Z: 0.0.

#### Definition 1.2. Feet parallel to the Z axis in appearance
The feet of a VRM model must be pointing straight and parallel to the Z axis.
The toes must be directed along the +Z axis.

For example, the feet must not be V-shaped.

#### Definition 1.3. The foot is grounded at Y: 0.0 in appearance
The height at which the VRM model is grounded, such as sole surfaces, is defined as Y: 0.0.

VRM models must be grounded on the plane at Y: 0.0 in appearance.
For example, it can be achieved by adjusting the transform of the Hips bone up and down.

Users of a VRM model can consider the model's Y: 0.0 as the ground plane.
For example, the relative position of the ground plane from the foot bone can be pre-calculated to calculate the ground plane in realtime.

#### Definition 1.4. The arms are extended along the X axis and are parallel to the ground in appearance
The arms must be extended in parallel to the X axis, which is also parallel to the ground.

Because the arms are extended horizontally, it forms the T letter by seeing from the front, and is referred to as a T-pose.

#### Definition 1.5. Shoulders are relaxed and lowest in appearance
If shoulder bones are present, the shoulders must be relaxed and lowermost in appearance.

This is to ensure that there is no difference between models without shoulder bones and with shoulder bones.
This also makes it easier to calculate how the shoulders look when the arms are down.

Combining with the Definition 1.4, this would result in a pose where the shoulders are lowered and the arms are raised, which is not possible for a real person, but it is the definition.

#### Definition 1.6. The hands are extended along the X axis and are parallel to the ground in appearance
The hands must be extended in parallel to the X axis, which is also parallel to the ground.

The palm plane must toward the -Y axis.

#### Definition 1.7. The four fingers are extended along the X axis and are parallel to the ground in appearance
The four fingers, index, middle, ring, and little, of each hand must be in parallel to the X axis, which is also parallel to the ground.

The nail surface must toward the +Y axis.

#### Definition 1.8. The thumb of each hand is extended between the X axis and +Z axis and is parallel to the ground in appearance
The thumb of each hand must be in parallel to the sum of X axis and +Z axis, which is 45 degrees from each axes, and it is also parallel to the ground.

The nail surface of the thumb is oriented with a 90 degrees outward roll, unlike the other four fingers.

The thumb of the left hand is directed between the +X axis and +Z axis, and the nail faces between the -X axis and the +Z axis.
The thumb of the right hand is directed between the -X axis and +Z axis, and the nail faces between the +X axis and the +Z axis.

### Definition based on numerical values of node transforms
A definition based solely on the appearance of the skinned mesh provides too many degrees of freedom in the possible postures of the bones.
Unnecessarily high degrees of freedom of models increase the complexity of implementation.
Therefore, we add a numerical-based definition for node transforms.

#### Definition 2.1. All node transforms are on a positive uniform scale
All node transforms must be in a positive uniform scale.
A positive uniform scale means that each value of the scale is the same non-zero positive value.
