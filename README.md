#!/bin/bash

pkg update && pkg upgrade -y
pkg install bash radare2 -y

cat << 'EOF' > /data/data/com.termux/files/home/bash_offset_analyzer.sh
#!/bin/bash

GREEN="\033[1;32m"
RED="\033[1;31m"
YELLOW="\033[1;33m"
CYAN="\033[1;36m"
MAGENTA="\033[1;35m"
RESET="\033[0m"

if ! command -v r2 &> /dev/null; then
    echo -e "${RED}Error: radare2 is not installed. Please install it first.${RESET}"
    exit 1
fi

show_main_menu() {
    clear
    echo -e "${MAGENTA}============================================="
    echo -e "${CYAN}            BASHA MOD Offset Analyzer"
    echo -e "=============================================${RESET}"
    echo -e "${YELLOW}1.${RESET} Analyze a specific offset in a .so file"
    echo -e "${YELLOW}2.${RESET} Analyze offsets in all .so files in a folder"
    echo -e "${YELLOW}3.${RESET} Exit"
}

analyze_specific_offset() {
    echo -e "${CYAN}Enter the path to the .so file:${RESET}"
    read -p "File: " file_path

    if [[ ! -f "$file_path" ]]; then
        echo -e "${RED}Error: File not found.${RESET}"
        return
    fi

    echo -e "${CYAN}Enter the offset (e.g., 0x29ff76):${RESET}"
    read -p "Offset: " offset

    echo -e "${CYAN}Opening radare2 for offset analysis...${RESET}"
    r2 -c "aaa; s $offset; VV" "$file_path"
}

analyze_offsets_in_folder() {
    echo -e "${CYAN}Enter the path to the folder containing .so files:${RESET}"
    read -p "Folder: " folder_path

    if [[ ! -d "$folder_path" ]]; then
        echo -e "${RED}Error: Folder not found.${RESET}"
        return
    fi

    echo -e "${CYAN}Enter the offset to analyze in each file (e.g., 0x29ff76):${RESET}"
    read -p "Offset: " offset

    for file in "$folder_path"/*.so; do
        if [[ -f "$file" ]]; then
            echo -e "${GREEN}Analyzing file: $file${RESET}"
            r2 -c "aaa; s $offset; VV" "$file"
        fi
    done

    echo -e "${CYAN}All files in the folder have been analyzed.${RESET}"
}

process_choice() {
    case $choice in
        1)
            analyze_specific_offset
            ;;
        2)
            analyze_offsets_in_folder
            ;;
        3)
            echo -e "${CYAN}Exiting. Goodbye!${RESET}"
            exit 0
            ;;
        *)
            echo -e "${RED}Invalid choice. Please try again.${RESET}"
            ;;
    esac
}

while true; do
    show_main_menu
    read -p "Enter your choice (1-3): " choice
    process_choice
done
EOF

chmod +x /data/data/com.termux/files/home/bash_offset_analyzer.sh
/data/data/com.termux/files/home/bash_offset_analyzer.sh
