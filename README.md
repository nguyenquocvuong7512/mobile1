import { useState, useMemo } from "react";
import { View, Text, FlatList, ScrollView, SafeAreaView, TouchableOpacity, ActivityIndicator, ImageBackground } from "react-native";
import productsData from "../../data/products1.json";
import Products1 from "../../components/Products1";

const CATEGORIES = [
  { id: "all", name: "Tất cả" },
  { id: "ao-thun", name: "Áo thun" },
  { id: "quan-jean", name: "Quần Jean" },
  { id: "so-mi", name: "Sơ mi" },
  { id: "quan-tay", name: "Quần tây" },
  { id: "giay-luoi", name: "Giày lưỡi" },
];

const SIZE = 6;

export default function KT() {
  const [category, setCategory] = useState("all");
  const [count, setCount] = useState(SIZE);
  const [loading, setLoading] = useState(false);

  const filtered = useMemo(() =>
    category === "all" ? productsData : productsData.filter(i => i.category === category),
    [category]);

  const products = useMemo(() => filtered.slice(0, count), [filtered, count]);

  const loadMore = () => {
    if (loading) return;
    setLoading(true);
    setTimeout(() => {
      setCount(c => c + SIZE);
      setLoading(false);
    }, 1000);
  };

  return (
    <ImageBackground source={require("../../assets/anhnen1.jpg")} className="flex-1" resizeMode="cover">
      <SafeAreaView className="flex-1">
        {/* HEADER */}
        <View className="flex-row items-center justify-between px-5 py-4">
          <TouchableOpacity><Text className="text-3xl text-white">☰</Text></TouchableOpacity>
          <View className="flex-row items-center">
            <Text className="text-xl mr-2">♙</Text>
            <Text className="text-xl font-bold text-white">Nguyễn Quốc Vương</Text>
          </View>
        </View>

        {/* TIÊU ĐỀ */}
        <Text className="text-2xl font-bold text-white px-5 py-5">DANH MỤC SẢN PHẨM</Text>

        {/* CATEGORY */}
        <View className="h-14 mb-4">
          <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ paddingHorizontal: 20 }}>
            {CATEGORIES.map(item => {
              const active = category === item.id;
              return (
                <TouchableOpacity
                  key={item.id}
                  onPress={() => { setCategory(item.id); setCount(SIZE); }}
                  className={`px-5 py-3 mr-3 rounded-2xl ${active ? "bg-green-600" : "bg-white/80"}`}
                >
                  <Text className={active ? "text-white font-bold" : "text-gray-800"}>{item.name}</Text>
                </TouchableOpacity>
              );
            })}
          </ScrollView>
        </View>

        {/* DANH SÁCH SẢN PHẨM */}
        <FlatList
          data={products}
          numColumns={2}
          renderItem={({ item }) => <Products1 item={item} />}
          keyExtractor={item => item.id.toString()}
          columnWrapperStyle={{ paddingHorizontal: 12, justifyContent: "space-between" }}
          contentContainerStyle={{ paddingBottom: 20 }}
        />

        {/* TẢI THÊM */}
        {products.length < filtered.length && (
          <TouchableOpacity onPress={loadMore} disabled={loading} className="bg-green-600 mx-5 mb-5 py-3 rounded-2xl">
            {loading ? <ActivityIndicator color="white" /> : <Text className="text-white text-center font-bold">Tải thêm sản phẩm</Text>}
          </TouchableOpacity>
        )}
      </SafeAreaView>
    </ImageBackground>
  );
}
