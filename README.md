# ---------------------------------------------------------------------------
# PWM Heat Soak Monitor v0.65
# Author: Linearburn
#
# Purpose:
# Estimate thermal equilibrium using heater bed PWM duty cycle rather than
# a fixed soak timer.
#
# Theory:
# As the printer frame, bed, and surrounding components absorb heat, the
# bed heater must provide additional energy to maintain temperature.
# Once the system approaches thermal equilibrium, heater power stabilizes
# and the average PWM duty cycle decreases.
#
# Implementation:
# - PWM sample interval is configurable with variable_sample_time.
# - Timeout is configurable with variable_max_checks.
# - A linear PWM estimate contributes 0-50% progress.
# - A stability check contributes the remaining 50%.
# - Stability increases by 1 on a valid sample.
# - Stability decreases by 1 on an invalid sample.
# - Stability is clamped between 0 and 5.
# - Progress is monotonic and will never decrease.
#
# Stability Criteria:
# PWM < variable_done_pwm
# PWM delta from previous sample < 0.05
#
# Completion Criteria:
# Stability reaches 5/5.
#
# Failure Criteria:
# variable_max_checks samples without completion.
# Default: 60 samples x 20 seconds = 20 minute timeout.
#
# # Tunables:
# variable_bed_temp     = Bed test temperature.
# variable_hotend_temp  = Hotend test temperature.
# variable_done_pwm     = PWM threshold used for stability detection.
# variable_sample_time  = Sampling interval in seconds.
# variable_max_checks   = Maximum number of samples before timeout.
#
# Internal Constants:
# high_pwm             = 0% progress reference point (currently fixed at 0.80).
# stability_delta      = Maximum allowed PWM change between samples (currently 0.05).
#
# Runtime Setters:
# SET_PWM_SOAK_TEMPS BED=80 HOTEND=150
# SET_PWM_SOAK_THRESHOLD PWM=0.20
# SET_PWM_SOAK_TIMEOUT INTERVAL=20 CHECKS=60
#
# Notes:
# This is intended as a proof-of-concept and should not be considered a
# scientifically accurate measurement of thermal equilibrium. The goal is
# to provide a practical adaptive alternative to fixed-duration heat soak
# timers.
# ---------------------------------------------------------------------------
