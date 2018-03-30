# ED_Subspace
## Requirements
Fortran(2003) + lapack
## Introduction
For a fermion (spin 1/2) system, we can study it by Exact Diagonalization (ED) of its Hamiltonian. After that, all the physical quantities can be calculated by the wave functions. This module provides methods to do this by the Hoseholder method (full space diagonalization). We can consider the symmetry of the system thus separate the Hamiltonian into different blocks(subspaces). We can study each subspace by this module. Compare with [LanczosSubspace](https://github.com/HengyueLi/LanczosSubspace) object, this object can be used to study the finite temperature system.
## Usage
Follow the steps in /Install.

## example
In the following example, a two-sites system will be diagonalized. The ground state energy and degeneracy are shown. The system contains a hopping term and onsite Coulomb energy.

    program main
      ! In this example, we consider a hopping between the two sites t=1,
      ! and also a onsite Coulomb interaction U = 4 (on each site).
      use ED_Subspace
      implicit none

      TYPE(EDSubSpace)::ED
      type(table)     :: Ta
      type(Ham)       :: H

      integer::hpara(8)
      complex*16::v
      character*16::Intp

      ! Initialization of table   (see https://github.com/HengyueLi/Fermion_Table )
      ! Here we use symmetry = 0 , so the subspace becomes the original Hilbert space.
      call ta%Initialization( ns = 2 ,  symmetry = 0 )




      ! Define the Hamiltonian of the system (see https://github.com/HengyueLi/FermionHamiltonian )
      call H%Initialization( ns = 2 )
      ! start append H terms  (needed)
      call h%StartAppendingInteraction()



      !! set the hopping term
      Intp     = "Hopping"
      hpara(1) = 0
      hpara(2) = 1
      v        = (1.0_8,0._8)
      call h%AppendingInteraction(InterType=Intp,InterPara=hpara,InterV=v)


      !! Set onsite Coulomb energy
      Intp     = "GlobalOnSiteU"
      v        = (4.0_8,0._8)
      call h%AppendingInteraction(InterType=Intp,InterPara=hpara,InterV=v)


      !Finished setting Hamiltonian
      call h%EndAppendingInteraction()


      ! Initialize the ED solver
      ! Ta :
      !    set the table
      ! IsReal:
      !    here we can set IsReal=.True. to increase speed because H is real. In more general case
      !    if H is complex number, we need to set IsReal=.False. .
      ! H :
      !   set the Hamiltonian
      ! Subid:
      !   Because in this system we have set symmetry = 0, there is only one subspace. So set subid = 1
      !   If in the case symmetry is not 0, there will be many subspaces. And this module can only to
      !   solver one subspace.(But you can use many of this objects to study all the spaces. However you do
      !   not need to do that because I have done this for your in another object). How to know which
      !   subspace are you interested?(how to set subid). You should check the usage of Table.
      !   You can find out method to fix the subspace you are interested in.
      !   For example you can chose a subspace that marked by total number of particle is 4,
      !   or by total number of spin up and spin down.
      Call ED%Initialization(Ta=Ta,IsReal=.True.,H=H,subid=1)


      !-diagonalization
      Call ED%diagonalization()

      !   let's check the energy of the first 5 states.
      !   All the states are ranged in a ascending order.
      write(*,*)"The energies of the first 5 states are:"
      write(*,*)ed%get_E(1)
      write(*,*)ed%get_E(2)
      write(*,*)ed%get_E(3)
      write(*,*)ed%get_E(4)
      write(*,*)ed%get_E(5)


      ! The two-sites system can be solved analytically. One can check that himself.

      ! More useful functions can be found in source code directly.


    end
