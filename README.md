```csharp
namespace KT1_Algorithms
{
    internal class Program
    {
        // Первая задача: Самая длинная последовательность номеров.
        // Хэш-таблица, потому что нам нужно быстро находить элементы из рандомного порядка.
        public static int FindLongestSeq(int[] nums)
        {
            int result = 0;
            HashSet<int> set = new HashSet<int>();

            for (int i = 0; i < nums.Length; i++)
                set.Add(nums[i]);

            for (int i = 0; i < nums.Length; i++)
            {
                if (!set.Contains(nums[i] - 1))
                {
                    int current = nums[i];
                    int length = 1;

                    while (set.Contains(current + 1))
                    {
                        length++;
                        current++;
                    }

                    if (length > result)
                        result = length;
                }
            }

            return result;
        }


        // Вторая задача: Найти все тройки чисел, сумма которых равна нулю.
        // 2 указателя, потому что мы можем отсортировать массив
        // и использовать два указателя, чтобы не перебирать все возможные комбинации троек.
        // Это не скользящее окно, потому что элементы, составляющие тройку, могут быть далеко друг от друга в массиве.
        public static List<int[]> FindThreesWithZeroSum(int[] nums)
        {
            var result = new List<int[]>();

            int[] sortedNums = new int[nums.Length];

            Array.Copy(nums, sortedNums, nums.Length);
            Array.Sort(sortedNums);

            for (int i = 0; i < sortedNums.Length - 2; i++)
            {
                if (i > 0 && sortedNums[i] == sortedNums[i - 1])
                    continue;

                int left = i + 1;
                int right = sortedNums.Length - 1;

                while (left < right)
                {
                    int sum = sortedNums[i] + sortedNums[left] + sortedNums[right];

                    if (sum == 0)
                    {
                        result.Add(new int[]
                        {
                            sortedNums[i],
                            sortedNums[left],
                            sortedNums[right]
                        });

                        left++;
                        right--;

                        while (left < right && sortedNums[left] == sortedNums[left - 1])
                            left++;

                        while (left < right && sortedNums[right] == sortedNums[right + 1])
                            right--;
                    }
                    else if (sum < 0)
                        left++;
                    else
                        right--;
                }
            }

            return result;
        }


        // Третья задача: Найти самую длинную последовательность из двух типов предметов.
        // Скользящее окно, потому что элементы, составляющие последовательность,
        // должны быть рядом друг с другом по условию задачи.
        public static int FindLongestSeqTwoTypesItems(int[] items)
        {
            int result = 0;

            Dictionary<int, int> typeCounts = new Dictionary<int, int>();
            int left = 0;

            for (int right = 0; right < items.Length; right++)
            {
                if (typeCounts.ContainsKey(items[right]))
                    typeCounts[items[right]]++;
                else
                    typeCounts[items[right]] = 1;

                while (typeCounts.Count > 2)
                {
                    if (typeCounts[items[left]] == 1)
                        typeCounts.Remove(items[left]);
                    else
                        typeCounts[items[left]]--;

                    left++;
                }

                if (result < right - left + 1)
                    result = right - left + 1;
            }

            return result;
        }


        // Четвертая задача: Найти количество подмассивов с заданной суммой.
        // Хэш-таблица + префиксные суммы, потому что нужно быстро находить
        // предыдущие суммы, которые дают нужную сумму подмассива.
        public static long FindSubArrWithGivenSum(int[] nums, long k)
        {
            long result = 0;

            Dictionary<long, long> prefixSum = new Dictionary<long, long>();
            prefixSum[0] = 1;

            long sum = 0;

            for (int i = 0; i < nums.Length; i++)
            {
                sum += nums[i];

                if (prefixSum.ContainsKey(sum - k))
                    result += prefixSum[sum - k];

                if (prefixSum.ContainsKey(sum))
                    prefixSum[sum]++;
                else
                    prefixSum[sum] = 1;
            }

            return result;
        }


        // Пятая задача: Найти максимальный объем воды между высотами.
        // 2 указателя, потому что нам нужно проверять пары высот с разных сторон,
        // чтобы найти максимальный объем.
        public static long FindMaxWaterVolumesBetweenHeight(int[] height)
        {
            long result = 0;

            int left = 0;
            int right = height.Length - 1;

            while (left < right)
            {
                long currentSquare = (long)Math.Min(height[left], height[right]) * (right - left);

                if (result < currentSquare)
                    result = currentSquare;

                if (height[left] < height[right])
                    left++;
                else
                    right--;
            }

            return result;
        }


        // Шестая задача: Найти минимальную подстроку, содержащую все символы из данной строки.
        // Скользящее окно, потому что ищем подстроку в пределах исходной строки,
        // которая содержит все символы из целевой строки.
        public static string FindMinSubStrWithNeededSymbols(string source, string target)
        {
            if (target.Length > source.Length)
                return "";

            Dictionary<char, int> need = new Dictionary<char, int>();
            Dictionary<char, int> window = new Dictionary<char, int>();

            foreach (char c in target)
            {
                if (need.ContainsKey(c))
                    need[c]++;
                else
                    need[c] = 1;
            }

            int left = 0;
            int formed = 0;
            int required = need.Count;

            int bestLength = int.MaxValue;
            int bestLeft = 0;

            for (int right = 0; right < source.Length; right++)
            {
                char c = source[right];

                if (window.ContainsKey(c))
                    window[c]++;
                else
                    window[c] = 1;

                if (need.ContainsKey(c) && window[c] == need[c])
                    formed++;

                while (formed == required)
                {
                    int currentLength = right - left + 1;

                    if (currentLength < bestLength)
                    {
                        bestLength = currentLength;
                        bestLeft = left;
                    }

                    char leftChar = source[left];
                    window[leftChar]--;

                    if (need.ContainsKey(leftChar) && window[leftChar] < need[leftChar])
                        formed--;

                    left++;
                }
            }

            if (bestLength == int.MaxValue)
                return "";

            return source.Substring(bestLeft, bestLength);
        }


        static void Main(string[] args)
        {
            // Задача 1
            int[] nums = { 100, 4, 200, 1, 3, 2, 0, 0, 5 };
            Console.WriteLine(FindLongestSeq(nums));


            // Задача 2
            int[] nums2 = { -1, 0, 1, 2, -1, -4 };

            var threes = FindThreesWithZeroSum(nums2);

            foreach (var three in threes)
                Console.WriteLine($"({three[0]}, {three[1]}, {three[2]})");


            // Задача 3
            int[] items = { 1, 1, 1, 2, 3 };
            Console.WriteLine(FindLongestSeqTwoTypesItems(items));


            // Задача 4
            int[] nums3 = { 1, 2, 3, 4, 5 };
            long k = 5;

            Console.WriteLine(FindSubArrWithGivenSum(nums3, k));


            // Задача 5
            int[] height = { 1, 8, 6, 2, 5, 4, 8, 3, 7 };
            Console.WriteLine(FindMaxWaterVolumesBetweenHeight(height));


            // Задача 6
            string source = "ADOBECODEBANC";
            string target = "ABC";

            Console.WriteLine(FindMinSubStrWithNeededSymbols(source, target));
        }
    }
}
```csharp
