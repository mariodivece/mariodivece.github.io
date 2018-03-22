---
layout: post
title: "Raspberry Pi's PWM Demystified (WIP)"
date: 2018-03-21
nocomments: false
area: "blog"
description: In this article, I cover the practical use of a 2-variable equation solver to find optimal parameters for the Raspberry Pi's native PWM driver which depends on on a clock divider and a range register. This has been the source of countless discussions in online forums and this article aims to clear some of the confusion caused by both, lack of information and the complexity of the problem.
tags: Embedded Electronics, Raspberry Pi, PWM, Math
---

*This post is still work in progress*

# Raspberry Pi's PWM Demystified

In this article, I cover the practical use of a 2-variable equation solver to find optimal parameters for the Raspberry Pi's native PWM driver which depends on on a clock divider and a range register. This has been the source of countless discussions in online forums and this article aims to clear some of the confusion caused by both, lack of information and the complexity of the problem.

## The Problem

The Raspberry Pi has a base clock for its 2-channel PWM controller running at 19.2 MHz. The Pi alows us to configure 2 parameters: a Clock Divider which goes from 2 to 4096, and a Range whcih goes from 2 to a 32-bit unsigned integer max value (4,294,967,295). 

## The Solutions

You can [download the CSV file with some preloaded solutions here](/img/rpi-pwm-frequencies.csv). I am posting some interesting frequencies in the table below.

| Frequency (Hz) | Period (µSecs) | Max Duty Cycle (percent) | Max Pulse Width (µSecs) | PWM Divider | PWM Range |
| ---: | ---: | ---: | ---: | ---: | ---: |
| 1 | 1,000,000.00 | 21.33% | 213,333.33 | `4000` | `4800` |
| 4 | 218,400.00 | 100.00% | 218,400.00 | `4095` | `1024` |
| 5 | 200,000.00 | 100.00% | 200,000.00 | `3750` | `1024` |
| 6 | 166,666.67 | 100.00% | 166,666.67 | `3125` | `1024` |
| 7 | 142,685.16 | 100.00% | 142,685.16 | `4095` | `669` |
| 8 | 124,983.39 | 100.00% | 124,983.39 | `2383` | `1007` |
| 9 | 111,092.40 | 100.00% | 111,092.40 | `4094` | `521` |
| 10 | 100,000.00 | 100.00% | 100,000.00 | `1875` | `1024` |
| 15 | 66,666.67 | 100.00% | 66,666.67 | `1250` | `1024` |
| 20 | 49,982.19 | 100.00% | 49,982.19 | `939` | `1022` |
| 30 | 33,333.33 | 100.00% | 33,333.33 | `625` | `1024` |
| 40 | 24,979.17 | 100.00% | 24,979.17 | `550` | `872` |
| 50 | 20,000.00 | 100.00% | 20,000.00 | `375` | `1024` |
| 60 | 16,640.00 | 100.00% | 16,640.00 | `312` | `1024` |
| 70 | 14,240.00 | 100.00% | 14,240.00 | `267` | `1024` |
| 80 | 12,491.88 | 100.00% | 12,491.88 | `237` | `1012` |
| 90 | 11,109.38 | 100.00% | 11,109.38 | `237` | `900` |
| 100 | 9,991.41 | 100.00% | 9,991.41 | `189` | `1015` |
| 150 | 6,666.67 | 100.00% | 6,666.67 | `125` | `1024` |
| 200 | 4,995.83 | 100.00% | 4,995.83 | `109` | `880` |
| 250 | 4,000.00 | 100.00% | 4,000.00 | `75` | `1024` |
| 300 | 3,332.66 | 100.00% | 3,332.66 | `77` | `831` |
| 400 | 2,499.32 | 100.00% | 2,499.32 | `47` | `1021` |
| 500 | 1,999.22 | 100.00% | 1,999.22 | `45` | `853` |
| 600 | 1,666.67 | 100.00% | 1,666.67 | `32` | `1000` |
| 700 | 1,427.34 | 100.00% | 1,427.34 | `27` | `1015` |
| 800 | 1,250.00 | 100.00% | 1,250.00 | `24` | `1000` |
| 900 | 1,110.16 | 100.00% | 1,110.16 | `21` | `1015` |
| 1,000 | 999.48 | 100.00% | 999.48 | `19` | `1010` |
| 1,500 | 666.56 | 100.00% | 666.56 | `18` | `711` |
| 2,000 | 500.00 | 100.00% | 500.00 | `10` | `960` |
| 2,500 | 400.00 | 100.00% | 400.00 | `8` | `960` |
| 3,000 | 333.33 | 100.00% | 333.33 | `20` | `320` |
| 4,000 | 250.00 | 100.00% | 250.00 | `5` | `960` |
| 5,000 | 200.00 | 100.00% | 200.00 | `4` | `960` |
| 6,000 | 166.67 | 100.00% | 166.67 | `4` | `800` |
| 7,002 | 142.81 | 100.00% | 142.81 | `3` | `914` |
| 8,000 | 125.00 | 100.00% | 125.00 | `3` | `800` |
| 9,001 | 111.09 | 100.00% | 111.09 | `3` | `711` |
| 10,000 | 100.00 | 100.00% | 100.00 | `2` | `960` |
| 12,000 | 83.33 | 100.00% | 83.33 | `2` | `800` |
| 15,000 | 66.67 | 100.00% | 66.67 | `2` | `640` |
| 16,000 | 62.50 | 100.00% | 62.50 | `2` | `600` |
| 19,200 | 52.08 | 100.00% | 52.08 | `2` | `500` |
| 20,000 | 50.00 | 100.00% | 50.00 | `2` | `480` |
| 25,000 | 40.00 | 100.00% | 40.00 | `2` | `384` |
| 30,000 | 33.33 | 100.00% | 33.33 | `2` | `320` |
| 38,019 | 26.30 | 100.00% | 26.30 | `5` | `101` |
| 38,400 | 26.04 | 100.00% | 26.04 | `2` | `250` |
| 40,000 | 25.00 | 100.00% | 25.00 | `2` | `240` |
| 44,036 | 22.71 | 100.00% | 22.71 | `2` | `218` |
| 48,000 | 20.83 | 100.00% | 20.83 | `2` | `200` |
| 50,000 | 20.00 | 100.00% | 20.00 | `2` | `192` |
| 60,000 | 16.67 | 100.00% | 16.67 | `2` | `160` |
| 75,000 | 13.33 | 100.00% | 13.33 | `2` | `128` |
| 96,000 | 10.42 | 100.00% | 10.42 | `2` | `100` |
| 100,000 | 10.00 | 100.00% | 10.00 | `2` | `96` |
| 150,000 | 6.67 | 100.00% | 6.67 | `2` | `64` |
| 160,000 | 6.25 | 100.00% | 6.25 | `2` | `60` |
| 192,000 | 5.21 | 100.00% | 5.21 | `2` | `50` |
| 200,000 | 5.00 | 100.00% | 5.00 | `2` | `48` |
| 240,000 | 4.17 | 100.00% | 4.17 | `2` | `40` |
| 256,000 | 3.91 | 100.00% | 3.91 | `3` | `25` |
| 300,000 | 3.33 | 100.00% | 3.33 | `2` | `32` |
| 320,000 | 3.13 | 100.00% | 3.13 | `2` | `30` |
| 384,000 | 2.60 | 100.00% | 2.60 | `2` | `25` |
| 400,000 | 2.50 | 100.00% | 2.50 | `2` | `24` |
| 480,000 | 2.08 | 100.00% | 2.08 | `2` | `20` |
| 600,000 | 1.67 | 100.00% | 1.67 | `2` | `16` |
| 640,000 | 1.56 | 100.00% | 1.56 | `2` | `15` |
| 768,000 | 1.30 | 100.00% | 1.30 | `5` | `5` |
| 800,000 | 1.25 | 100.00% | 1.25 | `2` | `12` |
| 960,000 | 1.04 | 100.00% | 1.04 | `2` | `10` |
| 1,200,000 | 0.83 | 100.00% | 0.83 | `2` | `8` |
| 1,280,000 | 0.78 | 100.00% | 0.78 | `3` | `5` |
| 1,600,000 | 0.63 | 100.00% | 0.63 | `2` | `6` |
| 1,920,000 | 0.52 | 100.00% | 0.52 | `2` | `5` |
| 2,400,000 | 0.42 | 100.00% | 0.42 | `2` | `4` |
| 3,200,000 | 0.31 | 100.00% | 0.31 | `2` | `3` |
| 4,800,000 | 0.21 | 100.00% | 0.21 | `2` | `2` |

## The Equation Solver

```csharp
namespace Unosquare.FrequencyFinder
{
namespace Unosquare.FrequencyFinder
{
    using System;
    using System.Collections.Concurrent;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    internal class Program
    {
        static void Main(string[] args)
        {
            var finder = new FrequencyParamsFinder();
            var targetFrequencies = new Dictionary<int, string>
            {
                { 1 , "A Standard Clock" },
                { 10, "10 Hz." },
                { 24, "Film" },
                { 50, "For Servos" },
                { 60, "TV" },
                { 38000, "IR Carrier" },
                { 44100, "CD Audio" },
                { 48000, "DVD Audio" },
                { 500000, "500 kHz" },
                { 1000000, "1 MHz" },
            };

            var frequencyExports = new List<FrequencyParams>(2048);

            foreach (var kvp in finder.StandardParams)
                frequencyExports.Add(kvp.Value);

            foreach (var kvp in targetFrequencies)
            {
                var parameters = finder.FindFrequencyParams(kvp.Key, 0.1, 0.04);
                var hrP = parameters.HighestResolution();
                var hdcP = parameters.BestDutyCycle();

                if (hrP != null)
                    frequencyExports.Add(hrP);

                if (hdcP != null & hdcP != hrP)
                    frequencyExports.Add(hdcP);
            }

            frequencyExports = frequencyExports.OrderBy(f => f.Frequency).ToList();
            var ght = frequencyExports.ToGitHubTable();
            var csv = frequencyExports.ToCsvString();

            Console.WriteLine(ght);
            Console.Write("Press any key to continue . . .");
            Console.ReadKey(intercept: true);
        }
    }

    /// <summary>
    /// A helper class that solves for Clock Divider and Range
    /// variables for the given frequency.
    /// </summary>
    public sealed class FrequencyParamsFinder
    {
        private const int DividerMin = 2;
        private const int DividerMax = 4096;
        private const int PulseLengthMin = 2;
        private const int PulseLengthMax = 1024;

        /// <summary>
        /// Initializes a new instance of the <see cref="FrequencyParamsFinder"/> class.
        /// </summary>
        public FrequencyParamsFinder()
        {
            var frequencyEntries = new ConcurrentBag<FrequencyParams>();

            // Compute and store all the whole frequencies
            Parallel.For(DividerMin, DividerMax, (divider) =>
            {
                for (var range = PulseLengthMin; range <= PulseLengthMax; range++)
                {
                    var entry = new FrequencyParams(divider, range);
                    if (entry.MaxDutyCycle < 1d)
                        continue;
                    else
                        frequencyEntries.Add(entry);
                }
            });

            var result = new Dictionary<double, FrequencyParams>(frequencyEntries.Count);
            foreach (var f in frequencyEntries)
            {
                var frequencyKey = f.FrequencyPartWhole;
                if (result.ContainsKey(frequencyKey))
                {
                    var existing = result[frequencyKey];

                    if (f.PulseLength > existing.PulseLength)
                    {
                        result[frequencyKey] = f;
                    }
                    else if (existing.FrequencyPartDecimal > f.FrequencyPartDecimal)
                    {
                        result[frequencyKey] = f;
                    }


                    continue;
                }

                result[frequencyKey] = f;
            }

            StandardParams = new SortedDictionary<double, FrequencyParams>(result);
        }

        /// <summary>
        /// Gets the standard frequency parameters.
        /// Standard frequency parameters are those that have a 100% possible duty cycle
        /// </summary>
        public SortedDictionary<double, FrequencyParams> StandardParams { get; }

        /// <summary>
        /// Finds the possible PWM parameters (Divider and Range) for the given frequency.
        /// The result may contain 0 or more suitable entries.
        /// </summary>
        /// <param name="frequency">The frequency to find paramenters for.</param>
        /// <param name="minDutyCycle">The minimum duty cycle as a percent (0.001, to 1.0).</param>
        /// <param name="threshold">The frequency threshold as a percent (0.0 to 0.99999).</param>
        /// <returns>0 or more compatible paramenters for the given frequency</returns>
        public FrequencyParams[] FindFrequencyParams(double frequency, double minDutyCycle = 1d, double threshold = 0.05)
        {
            // Parameter adjustments
            frequency = frequency.ClampValue(0.1, FrequencyParams.BaseFrequency / DividerMin / PulseLengthMin);
            minDutyCycle = minDutyCycle.ClampValue(0.001, 1);
            threshold = threshold.ClampValue(0, 0.9999);

            // Look in the standard frequencies
            if (StandardParams.ContainsKey(frequency))
                return new FrequencyParams[] { StandardParams[frequency] };

            var possibleEntries = new ConcurrentBag<FrequencyParams>();
            var minFrequency = frequency - (frequency * threshold);
            var maxFrequency = frequency + (frequency * threshold);
            var foundExact = false;
            var perfectEntry = default(FrequencyParams);

            Parallel.For(DividerMin, DividerMax, (divider) =>
            {
                for (var range = PulseLengthMin; range <= PulseLengthMax / minDutyCycle; range++)
                {
                    // break the loop if we have a perfect match
                    if (perfectEntry != null) break;

                    var entry = new FrequencyParams(frequency, divider, range);

                    // Do not consider adding if we don't have the minimum duty cycle
                    if (entry.MaxDutyCycle < minDutyCycle)
                        continue;

                    if (entry.Frequency == frequency && entry.MaxDutyCycle == 1d && entry.PulseLength == PulseLengthMax)
                    {
                        perfectEntry = entry;
                        possibleEntries.Add(entry);
                    }
                    else if (entry.Frequency == frequency)
                    {
                        possibleEntries.Add(entry);
                        foundExact = true;
                    }
                    else if (foundExact == false && entry.Frequency >= minFrequency && entry.Frequency <= maxFrequency)
                    {
                        possibleEntries.Add(entry);
                    }
                }
            });

            if (perfectEntry != null)
                return new FrequencyParams[] { perfectEntry };

            if (possibleEntries.Count <= 0)
                return new FrequencyParams[] { };

            var entries = foundExact ?
                possibleEntries.Where(e => e.Frequency == frequency).ToArray() :
                possibleEntries.ToArray();

            entries = entries
                .OrderBy(s => Math.Abs(s.Frequency - s.WantedFrequency))
                .ThenByDescending(s => s.PulseLength)
                .ThenByDescending(s => s.MaxDutyCycle)
                .ToArray();

            return entries;

        }
    }

    /// <summary>
    /// Estension methods for frequency finder
    /// </summary>
    public static class FrequencyFinderExtensions
    {
        /// <summary>
        /// Gets the frequency parameters with the most available steps (Pulse Length)
        /// </summary>
        /// <param name="parameters">The parameters.</param>
        /// <returns>The frequency parameters with the most available steps (Pulse Length)</returns>
        public static FrequencyParams HighestResolution(this IEnumerable<FrequencyParams> parameters)
        {
            if (parameters == null || parameters.Count() == 0)
                return null;

            return parameters
                .OrderBy(s => Math.Abs(s.Frequency - s.WantedFrequency))
                .ThenByDescending(s => s.PulseLength)
                .FirstOrDefault();
        }

        /// <summary>
        /// Gets the frequency parameters with the longest possible duty cycle
        /// </summary>
        /// <param name="parameters">The parameters.</param>
        /// <returns>The frequency parameters with the longest possible duty cycle</returns>
        public static FrequencyParams BestDutyCycle(this IEnumerable<FrequencyParams> parameters)
        {
            if (parameters == null || parameters.Count() == 0)
                return null;

            return parameters
                .OrderBy(s => Math.Abs(s.Frequency - s.WantedFrequency))
                .ThenByDescending(s => s.MaxDutyCycle)
                .FirstOrDefault();
        }

        /// <summary>
        /// Clamps the specified value between a minimum and a maximum.
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="value">The value.</param>
        /// <param name="min">The minimum.</param>
        /// <param name="max">The maximum.</param>
        /// <returns></returns>
        public static T ClampValue<T>(this T value, T min, T max)
            where T : struct, IComparable
        {
            if (value.CompareTo(min) < 0) return min;
            return value.CompareTo(max) > 0 ? max : value;
        }

        /// <summary>
        /// Creates the CSV string.
        /// </summary>
        /// <param name="fps">The frequency parameter entries.</param>
        /// <returns></returns>
        public static string ToCsvString(this IEnumerable<FrequencyParams> fps)
        {
            var builder = new StringBuilder();
            builder.Append("Frequency (Hz),");
            builder.Append("Period (µSecs),");
            builder.Append("Max Duty Cycle (Percent),");
            builder.Append("Max Pulse Width (µSecs),");
            builder.Append("PWM Divider,");
            builder.Append("PWM Range");
            builder.AppendLine();

            foreach (var p in fps)
            {
                builder.Append($"\"{p.FrequencyPartWhole:N0}\",");
                builder.Append($"\"{(p.Period * 1000000d):N2}\",");
                builder.Append($"\"{p.MaxDutyCycle:p}\",");
                builder.Append($"\"{(p.Period * p.MaxDutyCycle * 1000000d):N2}\",");
                builder.Append($"\"{p.Divider}\",");
                builder.Append($"\"{p.Range}\"");
                builder.AppendLine();
            }

            return builder.ToString();
        }

        /// <summary>
        /// Creates the GitHub data table.
        /// </summary>
        /// <param name="fps">The frequency parameter entries.</param>
        /// <returns></returns>
        public static string ToGitHubTable(this IEnumerable<FrequencyParams> fps)
        {
            var builder = new StringBuilder();
            builder.Append("|");
            builder.Append(" Frequency (Hz) |");
            builder.Append(" Period (µSecs) |");
            builder.Append(" Max Duty Cycle (Percent) |");
            builder.Append(" Max Pulse Width (µSecs) |");
            builder.Append(" PWM Divider |");
            builder.Append(" PWM Range |");
            builder.AppendLine();

            builder.Append("|");
            builder.Append(" ---: |");
            builder.Append(" ---: |");
            builder.Append(" ---: |");
            builder.Append(" ---: |");
            builder.Append(" ---: |");
            builder.Append(" ---: |");
            builder.AppendLine();

            foreach (var p in fps)
            {
                builder.Append("|");
                builder.Append($" {p.FrequencyPartWhole:N0} |");
                builder.Append($" {(p.Period * 1000000d):N2} |");
                builder.Append($" {p.MaxDutyCycle:p} |");
                builder.Append($" {(p.Period * p.MaxDutyCycle * 1000000d):N2} |");
                builder.Append($" `{p.Divider}` |");
                builder.Append($" `{p.Range}` |");
                builder.AppendLine();
            }

            return builder.ToString();
        }
    }

    /// <summary>
    /// Represents a set of frequency parameters.
    /// </summary>
    public sealed class FrequencyParams
    {
        /// <summary>
        /// The base frequency of 19.3 MHz
        /// </summary>
        public const double BaseFrequency = 19200000;

        /// <summary>
        /// The pulse length maximum value.
        /// </summary>
        public const double PulseLengthMax = 1024;

        /// <summary>
        /// Initializes a new instance of the <see cref="FrequencyParams" /> class.
        /// </summary>
        /// <param name="wantedFrequency">The wanted frequency.</param>
        /// <param name="divider">The divider.</param>
        /// <param name="range">The range.</param>
        public FrequencyParams(double wantedFrequency, int divider, int range)
        {
            Divider = divider;
            Range = range;
            var frequency = BaseFrequency / Convert.ToDouble(divider) / Convert.ToDouble(range);
            Frequency = frequency;
            Period = 1d / frequency;
            FrequencyPartWhole = Math.Truncate(frequency);
            FrequencyPartDecimal = frequency - FrequencyPartWhole;
            PulseLength = (int)Math.Min(PulseLengthMax, range);
            MaxDutyCycle = PulseLength / Range;
            WantedFrequency = wantedFrequency;
        }

        /// <summary>
        /// Initializes a new instance of the <see cref="FrequencyParams"/> class.
        /// </summary>
        /// <param name="divider">The divider.</param>
        /// <param name="range">The range.</param>
        public FrequencyParams(int divider, int range)
            : this(0, divider, range)
        {
            WantedFrequency = Frequency;
        }

        /// <summary>
        /// Gets the divider for the base clock.
        /// Should be from 2 to 4096
        /// </summary>
        public double Divider { get; }

        /// <summary>
        /// A 32-bit unsigend number representing the entire period
        /// </summary>
        public double Range { get; }

        /// <summary>
        /// Gets the frequency in Hz representing a whole cycle.
        /// </summary>
        public double Frequency { get; }

        /// <summary>
        /// Gets the whole part of the frequency.
        /// </summary>
        public double FrequencyPartWhole { get; }

        /// <summary>
        /// Gets the decimal part of the frequency.
        /// </summary>
        public double FrequencyPartDecimal { get; }

        /// <summary>
        /// Gets the frequency that creates these params.
        /// </summary>
        public double WantedFrequency { get; }

        /// <summary>
        /// Gets the period of the whole cycle (inverse of frequency).
        /// </summary>
        public double Period { get; }

        /// <summary>
        /// Gets the maximum duty cycle as a ratio of the PulseLength / Range.
        /// </summary>
        public double MaxDutyCycle { get; }

        /// <summary>
        /// Gets the maximum length of the pulse.
        /// Value goes from 1 to Range. Returns a maximum of 1024.
        /// </summary>
        public int PulseLength { get; }

        /// <summary>
        /// Returns a <see cref="System.String" /> that represents this instance.
        /// </summary>
        /// <returns>
        /// A <see cref="System.String" /> that represents this instance.
        /// </returns>
        public override string ToString()
        {
            return $"Frequency: {Frequency,12:0.00} Hz. | Divider: {Divider,6:0} | Range: {Range,6:0} | Duty Cycle: {MaxDutyCycle,7:p} | Max Value: {PulseLength,6:0}";
        }
    }
}
```
