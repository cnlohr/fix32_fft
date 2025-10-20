# fix32_fft

Single-file-header 32-bit-fixed-point in-place Fast Fourier Transform "fix32_fft.h" designed with special consideratinon for running on RISC-V and ARM processors.

This comes from the 16-bit fix_fft.c lineage, but is basically a rewrite.  I just LOVED their approach and interface.  You can see a copy of the original here: https://gist.github.com/Tomwi/3842231.

The original (unlicensed version is)
  Written by:  Tom Roberts  11/8/89
  Made portable:  Malcolm Slaney 12/15/94 malcolm@interval.com
  Enhanced:  Dimitrios P. Bouras  14 Jun 2006 dbouras@ieee.org (and license permission given)

Interface: 
```c
// Perform a real-only FFT
int fix32_fftr( int32_t fr[], int m, int inverse );

// Perform a full FFT.
int fix32_fft( int32_t fr[], int32_t fi[], int m, int inverse );

// Multiply 2 32-bit numbers
inline int32_t FIX_MPY(int32_t a, int32_t b)

// To keep the FFT in range, you will need to decimate the values.
int fix32_decimate( int32_t * fr, int32_t * fi, int m, int decimate ); 

Options:

//  Do precise rounding in multiply. This prevents a tendency towards zero. For
//  16-bit math (or smaller numbers) this matters a lot.  But if you scale your
//  inputs appropraitely, it does not really matter.
#define FIX32_FFT_PRECISEROUNDING 1

// Set this when including this file to include the body of the functions.
#define FIX32_FFT_IMPLEMENTATION
```

Usage:
```c
#define FIX32_FFT_IMPLEMENTATION
#define FIX32_FFT_PRECISEROUNDING 1

#include "fix32_fft.h"

#define M 12

int32_t real[1<<M] = { 0 };
int32_t imag[1<<M] = { 0 };

...
	// Fill in real and imag arrays.

	// Forward FFT 
	if( fix32_fft( real, imag, M, 0 ) ) goto fail;

	// Do stuff

	// Reverse FFT
	if( fix32_fft( real, imag, M, 1 ) ) goto fail;
...

```

## Departure from original version

The notable changes between versions are outlined below.

 * Original file was not licensed.
 * Original lineage was 16-bits - we expand this to 32-bits because most
   modern 32-bit processors have instructions like "mulh" which allow very
   fast 32x32 multiplies.  Additionally, 16 bits were insufficient for my
   purposes
 * Originally maxed out at 1024-wide FFTs. This implementation supports 
   up to 16777216-wide FFTs
 * Original code used a LUT for the sin/cos lookup.  This version uses a
   rotates between sin/cos valuse in the much smaller lookup.
 * Original code used shifting to prevent overflow.  Moving to 32-bits 
   obviates the need for the shifting logic.  We instead "shrink" on even
   phases and expand on odd phases.  By going back and forth we can much
   more effectively use the 
 * Original code used "precise" rounding to dither in the loss of the 
   lsb on multiplies. You can turn this off



## License note

If you are in a situation where you would like to license the code under the New BSD license, you may.  Both authors approve it use.

The original authors, Tom Roberts and Malcolm Slaney could not be reached for license concerns but it has departed significantly.

