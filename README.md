# rankBinaryTree
排序二叉树
package com.example.demo.tree;

import java.util.ArrayList;
import java.util.List;

public class RankBinaryTree {

    private TreeNode root;

    private List<Integer> _data = new ArrayList<>();


    public RankBinaryTree(int max) {
        root = new TreeNode();
        root.setHeight(1);
        root.setValueFrom(0);
        root.setValueTo(max + 1);
        root.LeftChildNode = CreateChildNode(root, 0, max / 2);
        root.RightChildNode = CreateChildNode(root, max / 2, max);
    }

    private TreeNode CreateChildNode(TreeNode current, int min, int max) {
        if (min == max) {
            return null;
        }
        TreeNode node = new TreeNode();
        node.setValueTo(max);
        node.setValueFrom(min);
        node.setHeight(current.Height + 1);
        node.Parent = current;
        int middle = (min + max) / 2;
        if (min < max - 1)
        {
            node.LeftChildNode = CreateChildNode(node, min, middle);
            node.RightChildNode = CreateChildNode(node, middle, max);
        }
        return node;
    }

    /// 往树中插入一个值
    public void Insert(int value) {
        InnerInsert(root, value);
        _data.add(value);
    }

    /// 子节点判断范围遍历插入
    private void InnerInsert(TreeNode node, int value) {
        if (node == null){
            return;
        }
        //判断是否在这个节点范围内
        if (value >= node.ValueFrom && value < node.ValueTo) {
            //更新节点总数信息
            node.Count++;
            //更新左子节点
            InnerInsert(node.LeftChildNode, value);
            //更新右子节点
            InnerInsert(node.RightChildNode, value);
        }
    }

    // 更新值，先移除再插入新值
    public void Update(int original, int diff) {
        Remove(original);
        int latest = original + diff;
        Insert(latest);
    }
    // 从树中移除一个值
    public void Remove(int value) {
        if (!_data.contains(value)){
            return;
        }
        InnerRemove(root, value);
    }

    // 移除值更新子节点信息
    public void InnerRemove(TreeNode node, int value) {
        if (node == null){
            return;
        }
        //判断是否在这个节点范围内
        if (value >= node.ValueFrom && value < node.ValueTo) {
            //更新总数信息
            node.Count--;
            //更新左子节点
            InnerRemove(node.LeftChildNode, value);
            //更新右子节点
            InnerRemove(node.RightChildNode, value);
        }
    }

    /// 从树中获取总排名
    public int GetRank(int value) {
        if (value < 0) {
            return 0;
        }
        return InnerGet(root, value);
    }

    // 遍历子节点获取累计排名
    private int InnerGet(TreeNode node, int value) {
        if (node.LeftChildNode == null || node.RightChildNode == null){
            return 1;
        }
        if (value >= node.LeftChildNode.ValueFrom && value < node.LeftChildNode.ValueTo) {
            //当这个值存在于左子节点中时，要累加右子节点的总数（表示这个数在多少名之后）
            return node.RightChildNode.Count + InnerGet(node.LeftChildNode, value);
        } else {
            //如果在右子节点中就继续遍历
            return InnerGet(node.RightChildNode, value);
        }
    }

    // 是否存在指定的值
    public boolean ContainValue(int value) {
        return _data.contains(value);
    }

    public static void main(String[] args) {
        Long createTreeStart = System.currentTimeMillis();
        RankBinaryTree rankBinaryTree = new RankBinaryTree(10000000);
        Long createTreeEnd = System.currentTimeMillis();
        System.out.println("创建树耗时:" + (createTreeEnd - createTreeStart));

        rankBinaryTree.Insert(1);
        rankBinaryTree.Insert(100);
        rankBinaryTree.Insert(10000);
        rankBinaryTree.Insert(127);
        rankBinaryTree.Insert(256);
        rankBinaryTree.Insert(1024);
        rankBinaryTree.Insert(985);
        rankBinaryTree.Insert(985);

        int i = rankBinaryTree.GetRank(256);
        System.out.println(i);

    }

}

