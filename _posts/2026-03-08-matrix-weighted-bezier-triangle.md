---
layout: post
title: "Matrix Weighted Bezier Triangle Patches"
date: 2026-03-08 12:00:00 +0800
categories: CAGD Bezier Surfaces
---

The reference is still Yang's paper
> Matrix weighted rational curves and surfaces

Combined with the algorithm from the previous blog post, we extend it again to triangular patches.

## Algorithm Description

Assume $P_{i,j,k}, 0\leq i,j,k\leq n, i+j+k=n$ are the given control points, and $M_{ijk}$ are the corresponding weight matrices. The matrix weighted rational triangular patch is defined by:

\[
Q_3(u,v,w)=[\sum_{i+j+k=n}M_{ijk}B^n_{i,j,k}(u,v,w)]^{-1}\sum_{i+j+k=n}M_{ijk}P_{i,j,k}B^n_{i,j,k}(u,v,w)
\]

where $(u,v)\in[u_{k_1}-1,u_{m+1}]\times[v_{k_2-1},v_{n+1}]$, and $B^n_{i,j,k}(u,v,w)=\frac{n!}{i!j!k!}$ is the Bernstein basis function defined on the triangular domain.

## Code Implementation

The following program written in MATLAB implements the function `wm_tri.m`

```matlab
function trepBer = wm_tri( controls,num,M,N )
%WM_TRI Summary of this function goes here
%   Detailed explanation goes here
points=cell(num,num);
for i=1:num
    for j=1:i
        points{i,j}=[(j-1)*(1/(num-1)),1-(i-1)*(1/(num-1)),(i-j)*(1/(num-1))];
    end
end

n=size(controls,1);

Bpoints=cell(num,num);
for i=1:num
    for j=1:i
        Bpoints{i,j}=[0;0;0];
        tempM=zeros(3);
        for u=1:n
            for v=1:u
                %i=v-1;j=n-u;k=u-v
                tempM=tempM+M{u+v-1}*factorial(n-1)/(factorial(v-1)*factorial(n-u)*factorial(u-v))*(points{i,j}(1)^(v-1)*points{i,j}(2)^(n-u)*points{i,j}(3)^(u-v));
                Bpoints{i,j}=Bpoints{i,j}+M{u+v-1}*controls{u,v}'*factorial(n-1)/(factorial(v-1)*factorial(n-u)*factorial(u-v))*(points{i,j}(1)^(v-1)*points{i,j}(2)^(n-u)*points{i,j}(3)^(u-v));
            end
        end
        Bpoints{i,j}=(tempM\Bpoints{i,j})';
    end
end

triConPoi=zeros(n*(n+1)/2,3);
temp=1;
for i=1:n
    for j=1:i
       triConPoi(temp,:)=controls{i,j};
       temp=temp+1;
    end
end

triConSur=zeros((n-1)^2,3);
temp=1;
for i=2:n%the first in the i-th line is n(n-1)/2
    for j=1:i-1
        %Counter clockwise order,up triangle
        triConSur(temp,:)=[i*(i-1)/2+j,i*(i-1)/2+j+1,i*(i-1)/2+j+1-i];
        temp=temp+1;
    end
end
for i=2:n-1%the first in the i-th line is n(n-1)/2
    for j=1:i-1
        %Counter clockwise order,down triangle
        triConSur(temp,:)=[i*(i-1)/2+j+1,i*(i-1)/2+j,i*(i-1)/2+j+1+i];
        temp=temp+1;
    end
end
trepCon=triangulation(triConSur,triConPoi);

triBezPoi=zeros(n*(n+1)/2,3);
temp=1;
for i=1:num
    for j=1:i
       triBezPoi(temp,:)=Bpoints{i,j};
       temp=temp+1;
    end
end
triBerSur=zeros((num-1)^2,3);
temp=1;
for i=2:num%the first in the i-th line is n(n-1)/2
    for j=1:i-1
        %Counter clockwise order,up triangle
        triBerSur(temp,:)=[i*(i-1)/2+j,i*(i-1)/2+j+1,i*(i-1)/2+j+1-i];
        temp=temp+1;
    end
end
for i=2:num-1%the first in the i-th line is n(n-1)/2
    for j=1:i-1
        %Counter clockwise order,down triangle
        triBerSur(temp,:)=[i*(i-1)/2+j+1,i*(i-1)/2+j,i*(i-1)/2+j+1+i];
        temp=temp+1;
    end
end
trepBer=triangulation(triBerSur,triBezPoi);

trisurf(trepCon,'edgecolor','k','FaceColor', 'interp');alpha(.2);axis equal;hold on;
trisurf(trepBer,'edgecolor','none','FaceColor', 'interp');alpha(.9);hold on;
for i=1:3
quiver3(triConPoi(i,1),triConPoi(i,2),triConPoi(i,3),N(i,1),N(i,2),N(i,3),'r','filled','LineWidth',2);hold on;
end

end
```

## Example

Let's plot one to see:

```matlab
controls = cell(2,2);
controls{1,1}=[0,0,0];
controls{2,1}=[0.5,1,-0.2];controls{2,2}=[-0.4,0.9,0.1];

N=[0.1,-0.3,0.6;0.4,0.3,0.6;-0.3,0.4,0.4];

omega=ones(3,1);
niu=ones(3,1);
M=cell(3,1);

for i=1:3
S=sqrt(N(i,1)^2+N(i,2)^2+N(i,3)^2);
N(i,1)=N(i,1)/S;
N(i,2)=N(i,2)/S;
N(i,3)=N(i,3)/S;
end

I=[1 0 0;0 1 0;0 0 1];
for i=1:3
M{i}=omega(i)*(I+niu(i)*N(i,:)'*N(i,:));
end

trepBer = wm_tri( controls,30,M,N);
```

## Results

<figure>
  <img src="https://img-blog.csdn.net/20171025101054014?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGFmZW5neGlhb3l1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="Matrix Weighted Bezier Triangle Patch Example" />
  <figcaption>Matrix Weighted Bezier Triangle Patch Example</figcaption>
</figure>

## Another Example

Let's look at a whole model, for example, a face model

|Original Mesh|Bezier Surface Patch|
|:-------------:|:-------------:|
|<img src="https://img-blog.csdn.net/20171025103123542?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGFmZW5neGlhb3l1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="Original Mesh" />| <img src="https://img-blog.csdn.net/20171025102752698?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGFmZW5neGlhb3l1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="Bezier Surface Patch" />|

Everything looks good!
